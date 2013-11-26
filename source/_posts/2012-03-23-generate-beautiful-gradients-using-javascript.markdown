---
layout: post
title: "Generate Beautiful Gradients Using JavaScript"
date: 2012-03-23 10:34
comments: true
categories: ['JavaScript','Highcharts']
---

I use Highcharts an excellent charting library built in JavaScript for much of my data visualization and analytic work. However, one thing that has always bugged me is their collection of default series colors, which are a bit limp. Highcharts does provide an easy way to manually override their defaults with your own list of colors. Unfortunately, it's not always possible to know in advance how many series will be supplied to the chart object. In the cases where there are more series than there are manually supplied colors the Highcharts default colors will start to leak into your charts and ruin the visual aesthetics. I wrote the gradient generator to produce a range of colors based on the parameters you supply. You simply supply start and stop colors and the number of steps in between, and you'll be sure to have enough colors for your series.

Even though I made this generator for a need I had in highcharts, you can use it anywhere you want a uniform sequence of colors. Here is how I use it in Highcharts:

``` javascript
var seriesColors = gradientFactory.generate({
    from: "#0000FF",
    to: ""#FF0000"
    stops: chartConfig.series.length
})
$.each(chartConfig.series,
function(i, v) {
    seriesOptions.push({
        color: seriesColors[i]
        // other attributes ...
    });
});
```

Here is the code for the Gradient Factory:

``` javascript
gradientFactory = (function() {
    var _beginColor = {
        red: 0,
        green: 0,
        blue: 0
    };
    var _endColor = {
        red: 255,
        green: 255,
        blue: 255
    };
    var _colorStops = 24;
    var _colors = [];
    var _colorKeys = ['red', 'green', 'blue'];
    var _rgbToHex = function(r, g, b) {
        return '#' + _byteToHex(r) + _byteToHex(g) + _byteToHex(b);
    };
    var _byteToHex = function(n) {
        var hexVals = "0123456789ABCDEF";
        return String(hexVals.substr((n >> 4) & 0x0F, 1)) + hexVals.substr(n & 0x0F, 1);
    };
    var _parseColor = function(color) {
        if ((color).toString() === "[object Object]") {
            return color;
        } else {
            color = (color.charAt(0) == "#") ? color.substring(1, 7) : color;
            return {
                red: parseInt((color).substring(0, 2), 16),
                green: parseInt((color).substring(2, 4), 16),
                blue: parseInt((color).substring(4, 6), 16)
            };
        }
    };
    var _generate = function(opts) {
        var _colors = [];
        var options = opts || {};
        var diff = {
            red: 0,
            green: 0,
            blue: 0
        };
        var len = _colorKeys.length;
        var pOffset = 0;
        if (typeof(options.from) !== 'undefined') {
            _beginColor = _parseColor(options.from);
        }
        if (typeof(options.to) !== 'undefined') {
            _endColor = _parseColor(options.to);
        }
        if (typeof(options.stops) !== 'undefined') {
            _colorStops = options.stops;
        }
        _colorStops = Math.max(1, _colorStops - 1);
        for (var x = 0; x < _colorStops; x++) {
            pOffset = parseFloat(x, 10) / _colorStops;
            for (var y = 0; y < len; y++) {
                diff[_colorKeys[y]] = _endColor[_colorKeys[y]] - _beginColor[_colorKeys[y]];
                diff[_colorKeys[y]] = (diff[_colorKeys[y]] * pOffset) + _beginColor[_colorKeys[y]];
            }
            _colors.push(_rgbToHex(diff.red, diff.green, diff.blue));
        }
        _colors.push(_rgbToHex(_endColor.red, _endColor.green, _endColor.blue));
        return _colors;
    };
    return {
        generate: _generate
    };
}).call(this);
```

Here are a few Jasmine Specs to prove it works:

``` javascript
describe("Gradient Generator",
function() {
  it("should generate a series of gradient colors",
  function() {
      expect($.hh.plugins.gradientFactory.generate({
          from: '#aed0ee',
          to: '#2a5980',
          stops: 2
      })).toEqual(['#AED0EE', '#2A5980']);
      expect($.hh.plugins.gradientFactory.generate({
          from: '#000000',
          to: '#999999',
          stops: 10
      })).toEqual(['#000000', '#111111', '#222222', '#333333', '#444444', '#555555', '#666666', '#777777', '#888888', '#999999']);
  });
});
```
A big tip of the hat to my friend jim bumgardner, who i [cribbed the color conversation functions from](http://krazydad.com/tutorials/makecolors.php).

 