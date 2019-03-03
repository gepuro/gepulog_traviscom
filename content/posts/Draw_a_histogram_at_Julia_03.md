---
title: Julia 0.3でヒストグラムを描く
date: 2014-06-20T22:09:30+09:00
draft: false
tags: [JuliaLang, 可視化]
aliases:
    - /archives/89
---

RDatasetsを読み込んで、ヒストグラムを描いてみました。
bincountでヒストグラムの粗さを指定出来ます。Rでいう<code>breaks</code>は無いのだろうか。

~~~{code}
julia> Pkg.add("RDatasets")
julia> using RDatasets
julia> using Gadfly
julia> iris = dataset("datasets", "iris")
julia> plot(x=iris[:SepalLength], Geom.histogram(bincount=30))
~~~

<div>
<script charset="utf-8">
        

// Minimum and maximum scale extents
var MIN_SCALE = 1.0/3.0;
var MAX_SCALE = 10.0;


// Traverse upwards from a d3 selection to find and return the first
// node with "plotroot" class.
var getplotroot = function(selection)  {
    var node = selection.node();
    while (node && node.classList && !node.classList.contains("plotroot")) {
        node = node.parentNode;
    }
    return d3.select(node);
};


// Construct a callback for toggling geometries on/off using color groupings.
//
// Args:
//   colorclass: class names assigned to geometries belonging to a paricular
//               color group.
//
// Returns:
//   A callback function.
//
var guide_toggle_color = function(colorclass) {
    var visible = true;
    return (function() {
        var root = getplotroot(d3.select(this));
        if (visible) {
            d3.select(this)
              .transition()
              .duration(250)
              .style("opacity", 0.5);
            root.selectAll(".geometry." + colorclass)
                .transition()
                .duration(250)
                .style("opacity", 0);
            visible = false;
        } else {
            d3.select(this)
              .transition()
              .duration(250)
              .style("opacity", 1.0);
            root.selectAll(".geometry." + colorclass)
                .transition()
                .duration(250)
                .style("opacity", 1.0);
            visible = true;
        }
    });
};


// Construct a callback used to toggle highly-visibility grid lines.
//
// Args:
//   color: Faded-in/faded-out color, respectively.
//
// Returns:
//   Callback function.
//
var guide_background_mouseover = function(color) {
    return (function () {
        var root = getplotroot(d3.select(this));
        root.selectAll(".xgridlines, .ygridlines")
            .transition()
            .duration(250)
            .attr("stroke", color);

        root.selectAll(".zoomslider")
            .transition()
            .duration(250)
            .attr("opacity", 1.0);
    });
};

var guide_background_mouseout = function(color) {
    return (function () {
        var root = getplotroot(d3.select(this));
        root.selectAll(".xgridlines, .ygridlines")
            .transition()
            .duration(250)
            .attr("stroke", color);

        root.selectAll(".zoomslider")
            .transition()
            .duration(250)
            .attr("opacity", 0.0);
    });
};


// Construct a call back used for mouseover effects in the point geometry.
//
// Args:
//   scale: Scale for expanded width
//   ratio: radius / line-width. This is necessary to maintain relative width
//          at arbitraty levels of zoom
//
// Returns:
//  Callback function.
//
var geom_point_mouseover = function(scale, ratio) {
    return (function() {
        var lw = this.getAttribute('r') * ratio * scale
        d3.select(this)
          .transition()
          .duration(100)
          .style("stroke-width", lw + 'px', 'important');
    });
};

// Construct a call back used for mouseout effects in the point geometry.
//
// Args:
//   scale: Scale for expanded width
//   ratio: radius / line-width. This is necessary to maintain relative width
//          at arbitraty levels of zoom
//
// Returns:
//  Callback function.
//
var geom_point_mouseout = function(scale, ratio) {
    return (function() {
        var lw = this.getAttribute('r') * ratio
        d3.select(this)
          .transition()
          .duration(100)
          .style("stroke-width", lw + 'px', 'important');
    });
};

// Translate and scale geometry while trying to maintain scale invariance for
// certain ellements.
//
// Args:
//   root: d3 selection of the root plot group node.
//   t: A transform of the form {"scale": scale}
//   old_scale: The scaling factor applied prior to t.scale.
//
var set_geometry_transform = function(root, ctx, old_scale) {
    var xscalable = root.node().classList.contains("xscalable");
    var yscalable = root.node().classList.contains("yscalable");

    var xscale = 1.0;
    var tx = 0.0;
    if (xscalable) {
        xscale = ctx.scale;
        tx = ctx.tx;
    }

    var yscale = 1.0;
    var ty = 0.0;
    if (yscalable) {
        yscale = ctx.scale;
        ty = ctx.ty;
    }

    root.selectAll(".geometry")
        .attr("transform",
          "translate(" + tx + " " + ty + ") " +
              "scale(" + xscale + " " + yscale + ")");

    var unscale_factor = old_scale / ctx.scale;

    // unscale geometry widths, radiuses, etc.
    var size_attribs = ["r"];
    var size_styles = ["font-size", "stroke-width"];
    root.select(".plotpanel")
        .selectAll("g, .geometry")
        .each(function() {
          sel = d3.select(this);
          var i;
          var key;
          var val;
          for (i in size_styles) {
              key = size_styles[i];
              val = sel.style(key);
              if (val !== null) {
                  // For some reason d3 rounds things like font-sizes to the
                  // nearest integer, so we are setting styles directly instead.
                  val = parseFloat(val);
                  sel.node().style.setProperty(key, unscale_factor * val + "px", "important");
              }
          }

          for (i in size_attribs) {
              key = size_attribs[i];
              val = sel.attr(key);
              if (val !== null) {
                  sel.attr(key, unscale_factor * val);
              }
          }
      });

    // TODO:
    // Is this going to work when we do things other than circles. Suppose we
    // have plots where we have a path drawing some sort of symbol which we want
    // to remain size-invariant. Should we be trying to place things using
    // translate?

    // move axis labels and grid lines around
    if (xscalable) {
        root.selectAll(".yfixed")
            .attr("transform", function() {
                return "translate(" + [ctx.tx, 0.0] + ") " +
                       "scale(" + [ctx.scale, 1.0] + ")";
          });

        root.selectAll(".xlabels")
            .attr("transform", function() {
              return "translate(" + [ctx.tx, 0.0] + ")";
          })
          .selectAll("text")
            .each(function() {
                d3.select(this).attr("x",
                    ctx.scale / old_scale * d3.select(this).attr("x"));
            });
    }

    if (yscalable) {
        root.selectAll(".xfixed")
            .attr("transform", function() {
              return "translate(" + [0.0, ctx.ty] + ") " +
                     "scale(" + [1.0, ctx.scale] + ")";
            });

        root.selectAll(".ylabels")
            .attr("transform", function() {
              return "translate(" + [0.0, ctx.ty] + ")";
            })
            .selectAll("text")
              .each(function() {
                  d3.select(this).attr("y",
                      ctx.scale / old_scale * d3.select(this).attr("y"));
            });
    }

    var bbox = root.select(".guide.background")
                   .select("path").node().getBBox();

    // hide/show ticks labels based on their position
    root.selectAll(".xlabels")
        .selectAll("text")
        .attr("visibility", function() {
            var x = parseInt(d3.select(this).attr("x"), 10) + ctx.tx;
            return bbox.x <= x && x <= bbox.x + bbox.width ? "visible" : "hidden";
        });

    root.selectAll(".ylabels")
        .selectAll("text")
        .attr("visibility", function() {
            var y = parseInt(d3.select(this).attr("y"), 10) + ctx.ty;
            return bbox.y <= y && y <= bbox.y + bbox.height ? "visible" : "hidden";
        });
};


// Construct a callback used for zoombehavior.
//
// Args:
//   t: A transform of the form {"scale": scale} to close arround.
//
// Returns:
//   A zoom behavior.
//
var zoom_behavior = function(ctx) {
    var zm = d3.behavior.zoom();
    ctx.zoom_behavior = zm;

    zm.scaleExtent([MIN_SCALE, MAX_SCALE])
      .on("zoom", function(d, i) {
        var root = getplotroot(d3.select(this));
        old_scale = ctx.scale;
        ctx.scale = d3.event.scale;
        var bbox = root.select(".guide.background")
                       .select("path").node().getBBox();

        var x_min = -bbox.width * ctx.scale - (ctx.scale * bbox.width - bbox.width);
        var x_max = bbox.width * ctx.scale;
        var y_min = -bbox.height * ctx.scale - (ctx.scale * bbox.height - bbox.height);
        var y_max = bbox.height * ctx.scale;

        var x0 = bbox.x - ctx.scale * bbox.x;
        var y0 = bbox.y - ctx.scale * bbox.y;

        var tx = Math.max(Math.min(d3.event.translate[0] - x0, x_max), x_min);
        var ty = Math.max(Math.min(d3.event.translate[1] - y0, y_max), y_min);

        tx += x0;
        ty += y0;

        ctx.tx = tx;
        ctx.ty = ty;

        set_geometry_transform(
            root,
            {"tx": tx,
             "ty": ty,
             "scale": ctx.scale}, old_scale);
        zm.translate([tx, ty]);

        update_zoomslider(root, ctx);
      });


    return (function (g) {
        zm(g);
        default_handler = g.on("wheel.zoom");
        function wheelhandler() {
        if (d3.event.shiftKey) {
                default_handler.call(this);
                d3.event.stopPropagation();
            }
        }
        g.on("wheel.zoom", wheelhandler)
         .on("mousewheel.zoom", wheelhandler)
         .on("DOMMouseScroll.zoom", wheelhandler);
    });
};


var slider_position_from_scale = function(scale) {
    if (scale >= 1.0) {
        return 0.5 + 0.5 * (Math.log(scale) / Math.log(MAX_SCALE));
    }
    else {
        return 0.5 * (Math.log(scale) - Math.log(MIN_SCALE)) / (0 - Math.log(MIN_SCALE));
    }
};


// Construct a call
var zoomslider_behavior = function(ctx, min_extent, max_extent) {
    var drag = d3.behavior.drag();
    ctx.zoomslider_behavior = drag;
    ctx.min_zoomslider_extent = min_extent;
    ctx.max_zoomslider_extent = max_extent;

    drag.on("drag", function() {
        var xmid = (min_extent + max_extent) / 2;
        var new_scale;

        // current slider posisition
        var xpos = slider_position_from_scale(ctx.scale) +
            (d3.event.dx / (max_extent - min_extent));

        // new scale
        if (xpos >= 0.5) {
            new_scale = Math.exp(2.0 * (xpos - 0.5) * Math.log(MAX_SCALE));
        }
        else {
            new_scale = Math.exp(2.0 * xpos * (0 - Math.log(MIN_SCALE)) +
                Math.log(MIN_SCALE));
        }
        new_scale = Math.min(MAX_SCALE, Math.max(MIN_SCALE, new_scale));

        // update scale
        var root = getplotroot(d3.select(this));
        var new_trans = scale_centered_translation(root, ctx, new_scale);

        ctx.zoom_behavior.scale(new_scale);
        ctx.zoom_behavior.translate(new_trans);
        ctx.zoom_behavior.event(root);

        // Note: the zoom event will take care of repositioning the slider thumb
    });

    drag.on("dragstart", function() {
        d3.event.sourceEvent.stopPropagation();
    });

    return drag;
};


// Reposition the zoom slider thumb based on the current scale
var update_zoomslider = function(root, ctx) {
    var xmid = (ctx.min_zoomslider_extent + ctx.max_zoomslider_extent) / 2;
    var xpos = ctx.min_zoomslider_extent +
        ((ctx.max_zoomslider_extent - ctx.min_zoomslider_extent) *
            slider_position_from_scale(ctx.scale));
    root.select(".zoomslider_thumb")
        .attr("transform", "translate(" + (xpos - xmid) + " " + 0 + ")");
};


// Compute the translation needed to change the scale when keeping the plot
// centered.
scale_centered_translation = function(root, ctx, new_scale) {
    var bbox = root.select(".guide.background")
                   .select("path").node().getBBox();

    // how off from center the current view is
    var xoff = ctx.zoom_behavior.translate()[0] -
              (bbox.x * (1 - ctx.scale) + (bbox.width * (1 - ctx.scale)) / 2);
    var yoff = ctx.zoom_behavior.translate()[1] -
              (bbox.y * (1 - ctx.scale) + (bbox.height * (1 - ctx.scale)) / 2);

    // rescale offsets
    xoff = xoff * new_scale / ctx.scale;
    yoff = yoff * new_scale / ctx.scale;

    // adjust for the panel position being scaled
    var x_edge_adjust = bbox.x * (1 - new_scale);
    var y_edge_adjust = bbox.y * (1 - new_scale);

    return [xoff + x_edge_adjust + (bbox.width - bbox.width * new_scale) / 2,
            yoff + y_edge_adjust + (bbox.height - bbox.height * new_scale) / 2];
};


// jump to a new scale with a nice transition
var zoom_step = function(root, ctx, new_scale) {
    var bbox = root.select(".guide.background")
                   .select("path").node().getBBox();
    ctx.zoom_behavior.size([bbox.width, bbox.height]);
    new_trans = scale_centered_translation(root, ctx, new_scale);

    root.transition()
        .duration(250)
        .tween("zoom", function()  {
            var trans_interp = d3.interpolate(ctx.zoom_behavior.translate(), new_trans);
            var scale_interp = d3.interpolate(ctx.zoom_behavior.scale(), new_scale);
            return function (t) {
                ctx.zoom_behavior.translate(trans_interp(t))
                                 .scale(scale_interp(t));
                ctx.zoom_behavior.event(root);
            };
        });
};


// Handlers for clicking the zoom in or zoom out buttons.
var zoomout_behavior = function(ctx) {
    return (function() {
        var new_scale = Math.max(MIN_SCALE, ctx.scale / 1.5);
        var root = getplotroot(d3.select(this));
        zoom_step(root, ctx, new_scale);
        d3.event.stopPropagation();
    });
};


var zoomin_behavior = function(ctx) {
    return (function() {
        var new_scale = Math.min(MAX_SCALE, ctx.scale * 1.5);
        var root = getplotroot(d3.select(this));
        zoom_step(root, ctx, new_scale);
        d3.event.stopPropagation();
    });
};


var zoomslider_track_behavior = function(ctx, min_extent, max_extent) {
    return (function() {
        var xpos = slider_position_from_scale(ctx.scale);
        var bbox = this.getBBox();
        var xclick = (d3.mouse(this)[0] - bbox.x) / bbox.width;
        var root = getplotroot(d3.select(this));
        var new_scale;
        if (xclick < xpos) {
            new_scale = Math.max(MIN_SCALE, ctx.scale / 1.5);
            zoom_step(root, ctx, new_scale);
        }
        else {
            new_scale = Math.min(MAX_SCALE, ctx.scale * 1.5);
            zoom_step(root, ctx, new_scale);
        }
        d3.event.stopPropagation();
    });
};


// Mouseover effects for zoom slider
var zoomslider_button_mouseover = function(destcolor) {
    return (function() {
        d3.select(this)
          .selectAll(".button_logo")
          .transition()
          .duration(150)
          .attr("fill", destcolor);
    });
};


var zoomslider_thumb_mouseover = function(destcolor) {
    return (function() {
        d3.select(this)
          .transition()
          .duration(150)
          .attr("fill", destcolor);
    });
};

//@ sourceURL=gadfly.js

    </script>

    <div id="gadflyplot89"></div>
    <script charset="utf-8">
        function draw_with_data(data, parent_id) {
  var g = d3.select(parent_id)
            .append("svg")
              .attr("width", "120mm")
              .attr("height", "80mm")
              .attr("viewBox", "0 0 120 80")
              .attr("stroke-width", "0.5")
              .attr("style", "stroke:black;fill:black");
  g.append("defs");
  var ctx = {
      "scale": 1.0,
      "tx": 0.0,
      "ty": 0.0
  };
(function (g) {
  g.attr("stroke", "none")
   .attr("fill", "#000000")
   .attr("stroke-width", 0.3)
   .attr("font-family", "Helvetic,Arial,sans")
   .style("font-size", "3.88px");
  (function (g) {
    g.attr("class", "plotroot xscalable");
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#4C404B")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.18px")
       .attr("class", "guide ylabels");
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", -19)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("30");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 13.06)
         .attr("y", 19.3)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("15");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", 95.88)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-15");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", -57.29)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("45");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 13.06)
         .attr("y", 6.53)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("20");
   })
;
      g.append("svg:text")
         .attr("x", 13.06)
         .attr("y", 44.83)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("5");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", 121.41)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-25");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", 70.36)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-5");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", 108.65)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-20");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", -31.76)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("35");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 13.06)
         .attr("y", 32.06)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("10");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", -6.23)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("25");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 13.06)
         .attr("y", 57.59)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("0");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", 83.12)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-10");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 13.06)
           .attr("y", -44.53)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("40");
    })
;
      }(g.append("g")));
    }(g.append("g")));
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#4C404B")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.18px")
       .attr("class", "guide xlabels");
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", -54.51)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("1");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 207.37)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("12");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 231.18)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("13");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 40.72)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("5");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", -6.9)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("3");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", -102.13)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("-1");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 16.91)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("4");
   })
;
      g.append("svg:text")
         .attr("x", 64.53)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("6");
   })
;
      g.append("svg:text")
         .attr("x", 112.14)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("8");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 159.76)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("10");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 135.95)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("9");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 88.34)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("7");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", -78.32)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("0");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 183.57)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("11");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", -30.7)
           .attr("y", 64.71)
           .attr("text-anchor", "middle")
            .call(function(text) {
      text.text("2");
    })
;
      }(g.append("g")));
    }(g.append("g")));
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#362A35")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.88px");
      g.append("svg:text")
         .attr("x", 64.53)
         .attr("y", 73)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("x");
   })
;
    }(g.append("g")));
    (function (g) {
      g.on("mouseover", guide_background_mouseover("#C6C6C9"))
       .on("mouseout", guide_background_mouseout("#F0F0F3"))
       .call(zoom_behavior(ctx))
;
      (function (g) {
        d3.select("defs")
  .append("svg:clipPath")
    .attr("id", parent_id + "_clippath0")
    .append("svg:path")
      .attr("d", " M14.06,5 L 115 5 115 59.12 14.06 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath0)");
        (function (g) {
          g.attr("class", "guide background")
           .attr("stroke", "#F1F1F5")
           .attr("fill", "#FAFAFA")
           .attr("opacity", 1.00);
          g.append("svg:path")
             .attr("d", "M14.06,5 L 115 5 115 59.12 14.06 59.12 z");
        }(g.append("g")));
        (function (g) {
          g.attr("stroke", "#F0F0F3")
           .attr("stroke-width", 0.2)
           .attr("class", "guide ygridlines xfixed");
          g.append("svg:path")
             .attr("d", "M14.06,19.3 L 115 19.3");
          g.append("svg:path")
             .attr("d", "M14.06,-57.29 L 115 -57.29");
          g.append("svg:path")
             .attr("d", "M14.06,44.83 L 115 44.83");
          g.append("svg:path")
             .attr("d", "M14.06,70.36 L 115 70.36");
          g.append("svg:path")
             .attr("d", "M14.06,-31.76 L 115 -31.76");
          g.append("svg:path")
             .attr("d", "M14.06,-6.23 L 115 -6.23");
          g.append("svg:path")
             .attr("d", "M14.06,83.12 L 115 83.12");
          g.append("svg:path")
             .attr("d", "M14.06,-44.53 L 115 -44.53");
          g.append("svg:path")
             .attr("d", "M14.06,57.59 L 115 57.59");
          g.append("svg:path")
             .attr("d", "M14.06,32.06 L 115 32.06");
          g.append("svg:path")
             .attr("d", "M14.06,108.65 L 115 108.65");
          g.append("svg:path")
             .attr("d", "M14.06,121.41 L 115 121.41");
          g.append("svg:path")
             .attr("d", "M14.06,6.53 L 115 6.53");
          g.append("svg:path")
             .attr("d", "M14.06,95.88 L 115 95.88");
          g.append("svg:path")
             .attr("d", "M14.06,-19 L 115 -19");
        }(g.append("g")));
        (function (g) {
          g.attr("stroke", "#F0F0F3")
           .attr("stroke-width", 0.2)
           .attr("class", "guide xgridlines yfixed");
          g.append("svg:path")
             .attr("d", "M207.37,5 L 207.37 59.12");
          g.append("svg:path")
             .attr("d", "M40.72,5 L 40.72 59.12");
          g.append("svg:path")
             .attr("d", "M-102.13,5 L -102.13 59.12");
          g.append("svg:path")
             .attr("d", "M64.53,5 L 64.53 59.12");
          g.append("svg:path")
             .attr("d", "M159.76,5 L 159.76 59.12");
          g.append("svg:path")
             .attr("d", "M88.34,5 L 88.34 59.12");
          g.append("svg:path")
             .attr("d", "M183.57,5 L 183.57 59.12");
          g.append("svg:path")
             .attr("d", "M-30.7,5 L -30.7 59.12");
          g.append("svg:path")
             .attr("d", "M-78.32,5 L -78.32 59.12");
          g.append("svg:path")
             .attr("d", "M135.95,5 L 135.95 59.12");
          g.append("svg:path")
             .attr("d", "M112.14,5 L 112.14 59.12");
          g.append("svg:path")
             .attr("d", "M16.91,5 L 16.91 59.12");
          g.append("svg:path")
             .attr("d", "M-6.9,5 L -6.9 59.12");
          g.append("svg:path")
             .attr("d", "M231.18,5 L 231.18 59.12");
          g.append("svg:path")
             .attr("d", "M-54.51,5 L -54.51 59.12");
        }(g.append("g")));
      }(g.append("g")));
      (function (g) {
        d3.select("defs")
  .append("svg:clipPath")
    .attr("id", parent_id + "_clippath1")
    .append("svg:path")
      .attr("d", " M14.06,5 L 115 5 115 59.12 14.06 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath1)");
        (function (g) {
          g.attr("class", "plotpanel");
          (function (g) {
            g.attr("shape-rendering", "crispEdges")
             .attr("fill", "#00BFFF")
             .attr("stroke", "none");
            (function (g) {
              g.attr("id", "id585")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M106.91,57.59 L 109.76 57.59 109.76 55.04 106.91 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id583")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M101.19,57.59 L 104.05 57.59 104.05 55.04 101.19 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id581")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M95.48,57.59 L 98.33 57.59 98.33 55.04 95.48 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id579")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M89.76,57.59 L 92.62 57.59 92.62 55.04 89.76 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id577")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M84.05,57.59 L 86.91 57.59 86.91 47.38 84.05 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id575")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M78.34,57.59 L 81.19 57.59 81.19 32.06 78.34 32.06 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id573")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M72.62,57.59 L 75.48 57.59 75.48 39.72 72.62 39.72 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id571")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M66.91,57.59 L 69.77 57.59 69.77 47.38 66.91 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id569")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M61.19,57.59 L 64.05 57.59 64.05 49.93 61.19 49.93 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id567")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M55.48,57.59 L 58.34 57.59 58.34 37.17 55.48 37.17 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id565")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M49.77,57.59 L 52.62 57.59 52.62 24.4 49.77 24.4 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id563")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M44.05,57.59 L 46.91 57.59 46.91 47.38 44.05 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id561")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M38.34,57.59 L 41.2 57.59 41.2 16.74 38.34 16.74 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id559")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M32.63,57.59 L 35.48 57.59 35.48 52.48 32.63 52.48 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id557")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M26.91,57.59 L 29.77 57.59 29.77 55.04 26.91 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id556")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M24.05,57.59 L 26.91 57.59 26.91 47.38 24.05 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id558")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M29.77,57.59 L 32.63 57.59 32.63 47.38 29.77 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id560")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M35.48,57.59 L 38.34 57.59 38.34 44.83 35.48 44.83 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id562")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M41.2,57.59 L 44.05 57.59 44.05 34.61 41.2 34.61 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id564")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M46.91,57.59 L 49.77 57.59 49.77 55.04 46.91 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id566")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M52.62,57.59 L 55.48 57.59 55.48 42.27 52.62 42.27 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id568")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M58.34,57.59 L 61.19 57.59 61.19 39.72 58.34 39.72 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id570")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M64.05,57.59 L 66.91 57.59 66.91 26.96 64.05 26.96 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id572")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M69.77,57.59 L 72.62 57.59 72.62 34.61 69.77 34.61 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id574")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M75.48,57.59 L 78.34 57.59 78.34 44.83 75.48 44.83 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id576")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M81.19,57.59 L 84.05 57.59 84.05 49.93 81.19 49.93 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id578")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M86.91,57.59 L 89.76 57.59 89.76 55.04 86.91 55.04 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id580")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M92.62,57.59 L 95.48 57.59 95.48 47.38 92.62 47.38 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id582")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M98.33,57.59 L 101.19 57.59 101.19 57.59 98.33 57.59 z");
            }(g.append("g")));
            (function (g) {
              g.attr("id", "id584")
               .attr("class", "geometry");
              g.append("svg:path")
                 .attr("d", "M104.05,57.59 L 106.91 57.59 106.91 47.38 104.05 47.38 z");
            }(g.append("g")));
          }(g.append("g")));
        }(g.append("g")));
      }(g.append("g")));
      (function (g) {
        d3.select("defs")
  .append("svg:clipPath")
    .attr("id", parent_id + "_clippath2")
    .append("svg:path")
      .attr("d", " M14.06,5 L 115 5 115 59.12 14.06 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath2)");
        (function (g) {
          g.attr("stroke", "none")
           .attr("class", "guide zoomslider")
           .attr("opacity", 0.00);
          (function (g) {
            g.attr("stroke", "#6A6A6A")
             .attr("stroke-opacity", 0.00)
             .attr("stroke-width", 0.3)
             .attr("fill", "#EAEAEA")
             .on("click", zoomin_behavior(ctx))
.on("dblclick", function() { d3.event.stopPropagation(); })
.on("mouseover", zoomslider_button_mouseover("#cd5c5c"))
.on("mouseout", zoomslider_button_mouseover("#6a6a6a"))
;
            g.append("svg:path")
               .attr("d", "M108,8 L 112 8 112 12 108 12 z");
            (function (g) {
              g.attr("fill", "#6A6A6A")
               .attr("class", "button_logo");
              g.append("svg:path")
                 .attr("d", "M108.8,9.6 L 109.6 9.6 109.6 8.8 110.4 8.8 110.4 9.6 111.2 9.6 111.2 10.4 110.4 10.4 110.4 11.2 109.6 11.2 109.6 10.4 108.8 10.4 z");
            }(g.append("g")));
          }(g.append("g")));
          (function (g) {
            g.attr("fill", "#EAEAEA")
             .on("click", zoomslider_track_behavior(ctx, 82, 99));
            g.append("svg:path")
               .attr("d", "M88.5,8 L 107.5 8 107.5 12 88.5 12 z");
          }(g.append("g")));
          (function (g) {
            g.attr("fill", "#6A6A6A")
             .attr("class", "zoomslider_thumb")
             .call(zoomslider_behavior(ctx, 82, 99))
.on("mouseover", zoomslider_thumb_mouseover("#cd5c5c"))
.on("mouseout", zoomslider_thumb_mouseover("#6a6a6a"))
;
            g.append("svg:path")
               .attr("d", "M97,8 L 99 8 99 12 97 12 z");
          }(g.append("g")));
          (function (g) {
            g.attr("stroke", "#6A6A6A")
             .attr("stroke-opacity", 0.00)
             .attr("stroke-width", 0.3)
             .attr("fill", "#EAEAEA")
             .on("click", zoomout_behavior(ctx))
.on("dblclick", function() { d3.event.stopPropagation(); })
.on("mouseover", zoomslider_button_mouseover("#cd5c5c"))
.on("mouseout", zoomslider_button_mouseover("#6a6a6a"))
;
            g.append("svg:path")
               .attr("d", "M84,8 L 88 8 88 12 84 12 z");
            (function (g) {
              g.attr("fill", "#6A6A6A")
               .attr("class", "button_logo");
              g.append("svg:path")
                 .attr("d", "M84.8,9.6 L 87.2 9.6 87.2 10.4 84.8 10.4 z");
            }(g.append("g")));
          }(g.append("g")));
        }(g.append("g")));
      }(g.append("g")));
    }(g.append("g")));
  }(g.append("g")));
}(g.append("g")));
    d3.select(parent_id)
      .selectAll("path")
      .each(function() {
          var sw = parseFloat(window.getComputedStyle(this).getPropertyValue("stroke-width"));
          d3.select(this)
            .attr("vector-effect", "non-scaling-stroke")
            .style("stroke-width", sw + "mm");
      });
}

var data = [
];

var draw = function(parent_id) {
    draw_with_data(data, parent_id);
};

if ('undefined' !== typeof module) {
    module.exports = draw;
} else if ('undefined' !== typeof window) {
    window.draw = draw
}

    </script>
    <script charset="utf-8">
        draw("#gadflyplot89");
    </script>

</div>

