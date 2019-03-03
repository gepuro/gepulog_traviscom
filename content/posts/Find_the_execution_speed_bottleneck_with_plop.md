---
title: 実行速度のボトルネックをplopで見つける
date: 2015-09-05T15:02:45+09:00
draft: false
tags: [Python]
aliases:
    - /archives/128
---


### 2016/10/6の追記
~~~{.py}
python3 setup.py install
~~~
でインストールするだけで良く、下にあるソースコードの変更は不必要になった。

### 以降は2016/9/5に書いた記事

<p><a href="https://blogs.dropbox.com/tech/2012/07/plop-low-overhead-profiling-for-python/">Plop: Low-overhead profiling for Python</a>にあるようにdropboxの中の人が開発したplopというツールがある。</p>
<p>これを使えば、プログラムの累積実行時間をメソッド単位で計測でき、コールグラフも作成出来る。pythonが遅いと感じた時に、簡単にボトルネックになっている箇所を発見出来るので便利！</p>
<p>しかしながら、<a href="https://github.com/bdarnell/plop"><a href="https://github.com/bdarnell/plop" class="uri">https://github.com/bdarnell/plop</a></a>にあるコードはpython2系でしか動かないので、3系でも動くように修正した。</p>
<p>面倒だったので、計測用のコードしか修正してないです。可視化は2系のまま。</p>
<div class="section level2">
<h2>使い方</h2>
<div class="section level3">
<h3>計測</h3>
<pre><code>python3 -m plop.collector myscript.py</code></pre>
</div>
<div class="section level3">
<h3>可視化</h3>
<pre><code>python -m plop.viewer --datadir=/tmp</code></pre>
</div>
</div>
<div class="section level2">
<h2>コード</h2>
<div id="collector.py" class="section level3">
<h3>collector.py</h3>
<pre class="{text}"><code>from __future__ import with_statement
import collections
import signal
import sys
import _thread as thread
from plop import platform
import time

class Collector(object):
    MODES = {
        'prof': (platform.ITIMER_PROF, signal.SIGPROF),
        'virtual': (platform.ITIMER_VIRTUAL, signal.SIGVTALRM),
        'real': (platform.ITIMER_REAL, signal.SIGALRM),
        }

    def __init__(self, interval=0.01, mode='virtual'):
        self.interval = interval
        self.mode = mode
        assert mode in Collector.MODES
        timer, sig = Collector.MODES[self.mode]
        signal.signal(sig, self.handler)
        self.reset()

    def reset(self):
        # defaultdict instead of counter for pre-2.7 compatibility
        self.stack_counts = collections.defaultdict(int)
        self.samples_remaining = 0
        self.stopping = False
        self.stopped = False

        self.samples_taken = 0
        self.sample_time = 0

    def start(self, duration=30.0):
        self.stopping = False
        self.stopped = False
        self.samples_remaining = int(duration / self.interval)
        timer, sig = Collector.MODES[self.mode]
        platform.setitimer(timer, self.interval, self.interval)

    def stop(self):
        self.stopping = True
        self.wait()

    def wait(self):
        while not self.stopped:
            pass # need busy wait; ITIMER_PROF doesn't proceed while sleeping

    def handler(self, sig, current_frame):
        start = time.time()
        self.samples_remaining -= 1
        if self.samples_remaining &lt;= 0 or self.stopping:
            platform.setitimer(Collector.MODES[self.mode][0], 0, 0)
            self.stopped = True
            return
        current_tid = thread.get_ident()
        for tid, frame in sys._current_frames().items():
            if tid == current_tid:
                frame = current_frame
            frames = []
            while frame is not None:
                code = frame.f_code
                frames.append((code.co_filename, code.co_firstlineno, code.co_name))
                frame = frame.f_back
            self.stack_counts[tuple(frames)] += 1
        end = time.time()
        self.samples_taken += 1
        self.sample_time += (end - start)

    def filter(self, max_stacks):
        self.stack_counts = dict(sorted(self.stack_counts.items(), key=lambda kv: -kv[1])[:max_stacks])


def main():
    # TODO: more options, refactor this into somewhere shared
    # between tornado.autoreload and auto2to3
    if len(sys.argv) &gt;= 3 and sys.argv[1] == '-m':
        mode = 'module'
        module = sys.argv[2]
        del sys.argv[1:3]
    elif len(sys.argv) &gt;= 2:
        mode = &quot;script&quot;
        script = sys.argv[1]
        sys.argv = sys.argv[1:]
    else:
        print(&quot;usage: python -m plop.collector -m module_to_run&quot;)
        sys.exit(1)
    

    collector = Collector()
    collector.start(duration=3600)
    exit_code = 0
    try:
        if mode == &quot;module&quot;:
            import runpy
            runpy.run_module(module, run_name=&quot;__main__&quot;, alter_sys=True)
        elif mode == &quot;script&quot;:
            with open(script) as f:
                global __file__
                __file__ = script
                # Use globals as our &quot;locals&quot; dictionary so that
                # something that tries to import __main__ (e.g. the unittest
                # module) will see the right things.
                exec(f.read(), globals())
    except (SystemExit) as e:
        exit_code = e.code
    collector.stop()
    collector.filter(50)
    with open('/tmp/plop.out', 'w') as f:
        f.write(repr(dict(collector.stack_counts)))
    print(&quot;profile output saved to /tmp/plop.out&quot;)
    overhead = float(collector.sample_time) / collector.samples_taken
    print(&quot;overhead was %s per sample (%s%%)&quot; % (
        overhead, overhead / collector.interval))
    sys.exit(exit_code)


if __name__ == '__main__':
    main()</code></pre>
</div>
<div class="section level3">
<h3>変更点だけなら</h3>
<pre class="{text}"><code>--- plop/collector.py   2015-09-01 16:34:42.000000000 +0900
+++ /usr/local/lib/python3.4/dist-packages/plop/collector.py    2015-09-01 16:36:38.515175381 +0900
@@ -1,20 +1,17 @@
 from __future__ import with_statement
 import collections
-import os
 import signal
 import sys
-import thread
+import _thread as thread
+from plop import platform
 import time
-import argparse
-import plop.platform
-
 
 class Collector(object):
     MODES = {
-        'prof': (plop.platform.ITIMER_PROF, signal.SIGPROF),
-        'virtual': (plop.platform.ITIMER_VIRTUAL, signal.SIGVTALRM),
-        'real': (plop.platform.ITIMER_REAL, signal.SIGALRM),
-    }
+        'prof': (platform.ITIMER_PROF, signal.SIGPROF),
+        'virtual': (platform.ITIMER_VIRTUAL, signal.SIGVTALRM),
+        'real': (platform.ITIMER_REAL, signal.SIGALRM),
+        }
 
     def __init__(self, interval=0.01, mode='virtual'):
         self.interval = interval
@@ -22,11 +19,11 @@
         assert mode in Collector.MODES
         timer, sig = Collector.MODES[self.mode]
         signal.signal(sig, self.handler)
-        signal.siginterrupt(sig, False)
         self.reset()
 
     def reset(self):
-        self.stacks = list()
+        # defaultdict instead of counter for pre-2.7 compatibility
+        self.stack_counts = collections.defaultdict(int)
         self.samples_remaining = 0
         self.stopping = False
         self.stopped = False
@@ -39,7 +36,7 @@
         self.stopped = False
         self.samples_remaining = int(duration / self.interval)
         timer, sig = Collector.MODES[self.mode]
-        plop.platform.setitimer(timer, self.interval, self.interval)
+        platform.setitimer(timer, self.interval, self.interval)
 
     def stop(self):
         self.stopping = True
@@ -47,13 +44,13 @@
 
     def wait(self):
         while not self.stopped:
-            pass  # need busy wait; ITIMER_PROF doesn't proceed while sleeping
+            pass # need busy wait; ITIMER_PROF doesn't proceed while sleeping
 
     def handler(self, sig, current_frame):
         start = time.time()
         self.samples_remaining -= 1
         if self.samples_remaining &lt;= 0 or self.stopping:
-            plop.platform.setitimer(Collector.MODES[self.mode][0], 0, 0)
+            platform.setitimer(Collector.MODES[self.mode][0], 0, 0)
             self.stopped = True
             return
         current_tid = thread.get_ident()
@@ -65,135 +62,56 @@
                 code = frame.f_code
                 frames.append((code.co_filename, code.co_firstlineno, code.co_name))
                 frame = frame.f_back
-            self.stacks.append(frames)
+            self.stack_counts[tuple(frames)] += 1
         end = time.time()
         self.samples_taken += 1
         self.sample_time += (end - start)
 
-
-class CollectorFormatter(object):
-    &quot;&quot;&quot;
-    Abstract class for output formats
-    &quot;&quot;&quot;
-    def format(self, collector):
-        raise Exception(&quot;not implemented&quot;)
-
-    def store(self, collector, filename):
-        with open(filename, &quot;wb&quot;) as f:
-            f.write(self.format(collector))
-
-
-class PlopFormatter(CollectorFormatter):
-    &quot;&quot;&quot;
-    Formats stack frames for plop.viewer
-    &quot;&quot;&quot;
-    def __init__(self, max_stacks=50):
-        self.max_stacks = 50
-
-    def format(self, collector):
-        # defaultdict instead of counter for pre-2.7 compatibility
-        stack_counts = collections.defaultdict(int)
-        for frames in collector.stacks:
-            stack_counts[tuple(frames)] += 1
-        stack_counts = dict(sorted(stack_counts.iteritems(),
-                                   key=lambda kv: -kv[1])[:self.max_stacks])
-        return repr(stack_counts)
-
-
-class FlamegraphFormatter(CollectorFormatter):
-    &quot;&quot;&quot;
-    Creates Flamegraph files
-    &quot;&quot;&quot;
-    def format(self, collector):
-        output = &quot;&quot;
-        previous = None
-        previous_count = 1
-        for stack in collector.stacks:
-            current = self.format_flame(stack)
-            if current == previous:
-                previous_count += 1
-            else:
-                output += &quot;%s %d\n&quot; % (previous, previous_count)
-                previous_count = 1
-                previous = current
-        output += &quot;%s %d\n&quot; % (previous, previous_count)
-        return output
-
-    def format_flame(self, stack):
-        funcs = map(&quot;{0[2]} ({0[0]}:{0[1]})&quot;.format, reversed(stack))
-        return &quot;;&quot;.join(funcs)
+    def filter(self, max_stacks):
+        self.stack_counts = dict(sorted(self.stack_counts.items(), key=lambda kv: -kv[1])[:max_stacks])
 
 
 def main():
     # TODO: more options, refactor this into somewhere shared
     # between tornado.autoreload and auto2to3
-    parser = argparse.ArgumentParser(description=&quot;Plop: Python Low-Overhead Profiler&quot;,
-                                     prog=&quot;python -m plop.collector&quot;,
-                                     formatter_class=argparse.ArgumentDefaultsHelpFormatter)
-    parser.add_argument(&quot;--format&quot;, &quot;-f&quot;, help=&quot;Output format&quot;,
-                        choices=[&quot;plop&quot;, &quot;flamegraph&quot;], default=&quot;plop&quot;)
-    parser.add_argument(&quot;--module&quot;, &quot;-m&quot;, help=&quot;Execute target as a module&quot;,
-                        action=&quot;store_const&quot;, const=True, default=False)
-    parser.add_argument(&quot;--mode&quot;, help=&quot;Interval timer mode to use, see `man 2 setitimer`&quot;,
-                        choices=[&quot;prof&quot;, &quot;real&quot;, &quot;virtual&quot;], default=&quot;prof&quot;)
-    parser.add_argument(&quot;--interval&quot;, help=&quot;Timer interval in seconds&quot;, default=0.01, type=float)
-    parser.add_argument(&quot;--duration&quot;, help=&quot;Profiling duration in seconds&quot;, default=3600,
-                        type=int)
-    parser.add_argument(&quot;--max-stacks&quot;, help=(&quot;Number of most frequent stacks to store.&quot;
-                        &quot; Ignored for Flamegraph output.&quot;), type=int, default=50)
-    parser.add_argument(&quot;target&quot;, help=&quot;Module or script to run&quot;)
-    parser.add_argument(&quot;arguments&quot;, nargs=argparse.REMAINDER,
-                        help=&quot;Pass-through arguments for the profiled application&quot;)
-    args = parser.parse_args()
-    sys.argv = [args.target] + args.arguments
-
-    if args.format == &quot;flamegraph&quot;:
-        extension = &quot;flame&quot;
-        formatter = FlamegraphFormatter()
-    elif args.format == &quot;plop&quot;:
-        extension = &quot;plop&quot;
-        formatter = PlopFormatter(max_stacks=args.max_stacks)
+    if len(sys.argv) &gt;= 3 and sys.argv[1] == '-m':
+        mode = 'module'
+        module = sys.argv[2]
+        del sys.argv[1:3]
+    elif len(sys.argv) &gt;= 2:
+        mode = &quot;script&quot;
+        script = sys.argv[1]
+        sys.argv = sys.argv[1:]
     else:
-        sys.stderr.write(&quot;Unhandled output format: %s&quot; % args.format)
-        sys.stderr.flush()
+        print(&quot;usage: python -m plop.collector -m module_to_run&quot;)
         sys.exit(1)
+    
 
-    if not os.path.exists('profiles'):
-        os.mkdir('profiles')
-    filename = 'profiles/%s-%s.%s' % (args.target, time.strftime('%Y%m%d-%H%M-%S'),
-                                      extension)
-
-    collector = Collector(mode=args.mode, interval=args.interval)
-    collector.start(duration=args.duration)
+    collector = Collector()
+    collector.start(duration=3600)
     exit_code = 0
     try:
-        if args.module:
+        if mode == &quot;module&quot;:
             import runpy
-            runpy.run_module(args.target, run_name=&quot;__main__&quot;, alter_sys=True)
-        else:
-            with open(args.target) as f:
-                # Execute the script in our namespace instead of creating
-                # a new one so that something that tries to import __main__
-                # (e.g. the unittest module) will see names defined in the
-                # script instead of just those defined in this module.
+            runpy.run_module(module, run_name=&quot;__main__&quot;, alter_sys=True)
+        elif mode == &quot;script&quot;:
+            with open(script) as f:
                 global __file__
-                __file__ = args.target
-                # If __package__ is defined, imports may be incorrectly
-                # interpreted as relative to this module.
-                global __package__
-                del __package__
-                exec f.read() in globals(), globals()
-    except SystemExit, e:
+                __file__ = script
+                # Use globals as our &quot;locals&quot; dictionary so that
+                # something that tries to import __main__ (e.g. the unittest
+                # module) will see the right things.
+                exec(f.read(), globals())
+    except (SystemExit) as e:
         exit_code = e.code
     collector.stop()
-    if collector.samples_taken:
-        formatter.store(collector, filename)
-        print &quot;profile output saved to %s&quot; % filename
-        overhead = float(collector.sample_time) / collector.samples_taken
-        print &quot;overhead was %s per sample (%s%%)&quot; % (
-            overhead, overhead / collector.interval)
-    else:
-        print &quot;no samples collected; program was too fast&quot;
+    collector.filter(50)
+    with open('/tmp/plop.out', 'w') as f:
+        f.write(repr(dict(collector.stack_counts)))
+    print(&quot;profile output saved to /tmp/plop.out&quot;)
+    overhead = float(collector.sample_time) / collector.samples_taken
+    print(&quot;overhead was %s per sample (%s%%)&quot; % (
+        overhead, overhead / collector.interval))
     sys.exit(exit_code)</code></pre>
</div>
</div>


