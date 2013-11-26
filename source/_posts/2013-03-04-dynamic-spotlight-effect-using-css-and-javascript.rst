---
layout: post
title: "Dynamic Spotlight Effect Using CSS and JavaScript"
date: 2013-03-04 13:27
comments: true
categories: [JavaScript, CSS]
---

In casual gaming there is a convention whereby the player is introduced to the interface during the first play 
cycle. Typically, this involves a character from the game pointing out aspects of the interface and telling the 
player how to use it and why they should care. Ideally, you want to visually draw the attention of the player to 
the relevant component of the interface as the characters are explaining it. For this purpose I created a 
JavaScript class which will spotlight a portion of the screen using only CSS and JavaScript. Here is an example of 
it `working <http://jsfiddle.net/HFnYC/>`_.

The class allows you to configure the following spotlight attributes:

 * starting x,y (integer)
 * destination x,y (integer)
 * duration (0%-100%)
 * callback when animation is complete (function)

Below is the CSS and JavaScript you'll need to use it in your own projects. If you improve this script please let me
know.

.. code-block:: html

  <div id="spotLight"></div>

.. code-block:: css

  #spotLight {
    width:1024px;
    height:768px;
    z-index:9;
    position:absolute;
    display:none;
  }

.. code-block:: javascript
  
  function SpotLight(element) {
    this.element = element;
    this.x = element.width() / 2;
    this.y = element.height() / 2;
    this.show = function() {
      element.hide();
      element.removeClass("hide");
      return element.fadeIn('fast');
    };
    this.hide = function(callback) {
      element.fadeOut('fast', function() {
        if (callback) {
          return callback();
        }
      });
      return element.addClass("hide");
    };
    this.move = function(opts) {
      var endX, endY, obj;
      obj = $.extend({}, {
        start_x: this.x,
        start_y: this.y,
        x: this.x,
        y: this.y,
        aperture: "50%",
        duration: 1000,
        done: function() {}
      }, opts);
      endX = obj.x;
      endY = obj.y;
      obj.x = obj.start_x;
      obj.y = obj.start_y;
      return jQuery(obj).animate({
        x: endX,
        y: endY
      }, {
        duration: obj.duration,
        step: function() {
          var style, _i, _len, _ref;
          _ref = ["-moz-radial-gradient(" + this.x + "px " + this.y + "px, ellipse cover,  rgba(0,0,0,0) 0%, rgba(0,0,0,0.8) " + this.aperture + ", rgba(0,0,0,0.8) 100%)", "-webkit-gradient(radial, " + this.x + "px " + this.y + "px, 0px, " + this.x + "px " + this.y + "px, 100%, color-stop(0%,rgba(0,0,0,0)), color-stop(" + this.aperture + ",rgba(0,0,0,0.8)), color-stop(100%,rgba(0,0,0,0.8)))", "-webkit-radial-gradient(" + this.x + "px " + this.y + "px, ellipse cover,  rgba(0,0,0,0) 0%,rgba(0,0,0,0.8) " + this.aperture + ",rgba(0,0,0,0.8) 100%)", "-o-radial-gradient(" + this.x + "px " + this.y + "px, ellipse cover,  rgba(0,0,0,0) 0%,rgba(0,0,0,0.8) " + this.aperture + ",rgba(0,0,0,0.8) 100%)", "-ms-radial-gradient(" + this.x + "px " + this.y + "px, ellipse cover,  rgba(0,0,0,0) 0%,rgba(0,0,0,0.8) " + this.aperture + ",rgba(0,0,0,0.8) 100%)", "radial-gradient(ellipse at " + this.x + "px " + this.y + "px,  rgba(0,0,0,0) 0%,rgba(0,0,0,0.8) " + this.aperture + ",rgba(0,0,0,0.8) 100%)"];
          for (_i = 0, _len = _ref.length; _i < _len; _i++) {
            style = _ref[_i];
            element.css({
              "background": style
            });
          }
          return true;
        },
        done: obj.done
      });
    };
    return this;
  }
  
  // Example Usage:
  var spotLight = new SpotLight($("#spotLight"))
  spotLight.show();
  spotLight.move({ x: 150, y: 650 });
