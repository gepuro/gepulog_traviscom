---
title: Julia 0.3で箱ひげ図を描く
date: 2014-06-20T22:32:21+09:00
draft: false
tags: [JuliaLang, 可視化]
aliases:
    - /archives/90
---

データやパッケージのロードは、「[Julia 0.3でヒストグラムを描く](http://blog.gepuro.net/archives/89)」を参照してください。

箱ひげ図を描くには、次のようなコマンドを打つ。
~~~{code}
julia > plot(iris[[:SepalLength, :Species]], Geom.boxplot, x="Species", y="SepalLength")
~~~

Rでは変数が一つだけでも箱ひげ図を描くことが出来るけど、JuliaのGadflyではx軸とy軸を指定する必要があるみたい。また、x軸やy軸のラベル名も指定しないと動かなかった。

現状では、Rの方が手軽に作図出来るなあって印象です。

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

    <div id="gadflyplot90"></div>
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
    g.attr("class", "plotroot yscalable");
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#4C404B")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.18px")
       .attr("class", "guide ylabels");
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", 95.88)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("1");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", -44.53)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("12");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", -57.29)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("13");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 18.94)
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
           .attr("x", 18.94)
           .attr("y", 70.36)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("3");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", 121.41)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("-1");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 18.94)
         .attr("y", 57.59)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("4");
   })
;
      g.append("svg:text")
         .attr("x", 18.94)
         .attr("y", 32.06)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("6");
   })
;
      g.append("svg:text")
         .attr("x", 18.94)
         .attr("y", 6.53)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("8");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", -19)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("10");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", -6.23)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("9");
    })
;
      }(g.append("g")));
      g.append("svg:text")
         .attr("x", 18.94)
         .attr("y", 19.3)
         .attr("text-anchor", "end")
         .style("dominant-baseline", "central")
         .call(function(text) {
     text.text("7");
   })
;
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", 108.65)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("0");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", -31.76)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
            .call(function(text) {
      text.text("11");
    })
;
      }(g.append("g")));
      (function (g) {
        g.attr("visibility", "hidden");
        g.append("svg:text")
           .attr("x", 18.94)
           .attr("y", 83.12)
           .attr("text-anchor", "end")
           .style("dominant-baseline", "central")
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
         .attr("x", 8.65)
         .attr("y", 32.06)
         .attr("text-anchor", "middle")
         .style("dominant-baseline", "central")
         .attr("transform", "rotate(-90, 8.65, 32.06)")
         .call(function(text) {
     text.text("SepalLength");
   })
;
    }(g.append("g")));
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#4C404B")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.18px")
       .attr("class", "guide xlabels");
      g.append("svg:text")
         .attr("x", 35.78)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("setosa");
   })
;
      g.append("svg:text")
         .attr("x", 99.16)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("virginica");
   })
;
      g.append("svg:text")
         .attr("x", 67.47)
         .attr("y", 64.71)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("versicolor");
   })
;
    }(g.append("g")));
    (function (g) {
      g.attr("stroke", "none")
       .attr("fill", "#362A35")
       .attr("font-family", "'PT Sans','Helvetica Neue','Helvetica',sans-serif")
       .style("font-size", "3.88px");
      g.append("svg:text")
         .attr("x", 67.47)
         .attr("y", 73)
         .attr("text-anchor", "middle")
         .call(function(text) {
     text.text("Species");
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
      .attr("d", " M19.94,5 L 115 5 115 59.12 19.94 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath0)");
        (function (g) {
          g.attr("class", "guide background")
           .attr("stroke", "#F1F1F5")
           .attr("fill", "#FAFAFA")
           .attr("opacity", 1.00);
          g.append("svg:path")
             .attr("d", "M19.94,5 L 115 5 115 59.12 19.94 59.12 z");
        }(g.append("g")));
        (function (g) {
          g.attr("stroke", "#F0F0F3")
           .attr("stroke-width", 0.2)
           .attr("class", "guide ygridlines xfixed");
          g.append("svg:path")
             .attr("d", "M19.94,-44.53 L 115 -44.53");
          g.append("svg:path")
             .attr("d", "M19.94,44.83 L 115 44.83");
          g.append("svg:path")
             .attr("d", "M19.94,121.41 L 115 121.41");
          g.append("svg:path")
             .attr("d", "M19.94,32.06 L 115 32.06");
          g.append("svg:path")
             .attr("d", "M19.94,-19 L 115 -19");
          g.append("svg:path")
             .attr("d", "M19.94,19.3 L 115 19.3");
          g.append("svg:path")
             .attr("d", "M19.94,-31.76 L 115 -31.76");
          g.append("svg:path")
             .attr("d", "M19.94,83.12 L 115 83.12");
          g.append("svg:path")
             .attr("d", "M19.94,108.65 L 115 108.65");
          g.append("svg:path")
             .attr("d", "M19.94,-6.23 L 115 -6.23");
          g.append("svg:path")
             .attr("d", "M19.94,6.53 L 115 6.53");
          g.append("svg:path")
             .attr("d", "M19.94,57.59 L 115 57.59");
          g.append("svg:path")
             .attr("d", "M19.94,70.36 L 115 70.36");
          g.append("svg:path")
             .attr("d", "M19.94,-57.29 L 115 -57.29");
          g.append("svg:path")
             .attr("d", "M19.94,95.88 L 115 95.88");
        }(g.append("g")));
        (function (g) {
          g.attr("stroke", "#F0F0F3")
           .attr("stroke-width", 0.2)
           .attr("class", "guide xgridlines yfixed");
          g.append("svg:path")
             .attr("d", "M83.31,5 L 83.31 59.12");
          g.append("svg:path")
             .attr("d", "M51.62,5 L 51.62 59.12");
        }(g.append("g")));
      }(g.append("g")));
      (function (g) {
        d3.select("defs")
  .append("svg:clipPath")
    .attr("id", parent_id + "_clippath1")
    .append("svg:path")
      .attr("d", " M19.94,5 L 115 5 115 59.12 19.94 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath1)");
        (function (g) {
          g.attr("class", "plotpanel");
          (function (g) {
            g.attr("class", "geometry");
            (function (g) {
              g.attr("fill", "#00BFFF")
               .attr("stroke", "#0096DD");
              g.append("svg:circle")
                .attr("cx", 99.16).attr("cy", 46.1).attr("r", 0.6)
              ;
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M93.87,7.81 L 104.44 7.81");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M93.87,37.17 L 104.44 37.17");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M99.16,20.57 L 99.16 7.81");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M99.16,29.19 L 99.16 37.17");
            }(g.append("g")));
            (function (g) {
              g.attr("fill", "#00BFFF")
               .attr("stroke", "#0096DD")
               .attr("stroke-width", 0.3);
              g.append("svg:path")
                 .attr("d", "M83.81,29.19 L 114.5 29.19 114.5 20.57 83.81 20.57 z");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M62.19,19.3 L 72.75 19.3");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M62.19,46.1 L 72.75 46.1");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M67.47,28.23 L 67.47 19.3");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M67.47,37.17 L 67.47 46.1");
            }(g.append("g")));
            (function (g) {
              g.attr("fill", "#00BFFF")
               .attr("stroke", "#0096DD")
               .attr("stroke-width", 0.3);
              g.append("svg:path")
                 .attr("d", "M52.12,37.17 L 82.81 37.17 82.81 28.23 52.12 28.23 z");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M30.5,34.61 L 41.06 34.61");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M30.5,53.76 L 41.06 53.76");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M35.78,42.27 L 35.78 34.61");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.3)
               .attr("stroke", "#0096DD");
              g.append("svg:path")
                 .attr("d", "M35.78,47.38 L 35.78 53.76");
            }(g.append("g")));
            (function (g) {
              g.attr("fill", "#00BFFF")
               .attr("stroke", "#0096DD")
               .attr("stroke-width", 0.3);
              g.append("svg:path")
                 .attr("d", "M20.44,47.38 L 51.12 47.38 51.12 42.27 20.44 42.27 z");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.6)
               .attr("stroke", "#34FFFF");
              g.append("svg:path")
                 .attr("d", "M93.87,25.68 L 104.44 25.68");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.6)
               .attr("stroke", "#34FFFF");
              g.append("svg:path")
                 .attr("d", "M62.19,33.34 L 72.75 33.34");
            }(g.append("g")));
            (function (g) {
              g.attr("stroke-width", 0.6)
               .attr("stroke", "#34FFFF");
              g.append("svg:path")
                 .attr("d", "M30.5,44.83 L 41.06 44.83");
            }(g.append("g")));
          }(g.append("g")));
        }(g.append("g")));
      }(g.append("g")));
      (function (g) {
        d3.select("defs")
  .append("svg:clipPath")
    .attr("id", parent_id + "_clippath2")
    .append("svg:path")
      .attr("d", " M19.94,5 L 115 5 115 59.12 19.94 59.12 z");g.attr("clip-path", "url(#" + parent_id + "_clippath2)");
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
        draw("#gadflyplot90");
    </script>
</div>

