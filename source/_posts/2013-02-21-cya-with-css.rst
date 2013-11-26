---
layout: post
title: "CYA With CSS"
date: 2013-02-21 12:27
comments: true
categories: [CSS]
---

Using Design Time Classes To Polish Your Product
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This post is dedicated to *CYA* with *CSS*; for the uninitiated CYA means "cover your ass", and I assume that anyone reading my blog already
knows what CSS is. Just as you can craft the JavaScript on your website to act defensively against unforeseen errors, so too can you use 
CSS at the design stage to ensure you don't end up with egg on your face post-launch.

A while back, I was viewing the Github's source code (man I sound like such a nerd), and I noticed these classes added to their body
tags: "*logged_in page-dashboard macintosh  env-production*". Several of these classes are obviously progressive enhancement style
additions meant to change the layout / features of the page based on the visitor's browser. In my own sites I often include the controller
and action params into the body tag so that I can scope my JavaScript and CSS executions. Doing this provides a convenient way to namespace
your CSS and JS, without having to worry about polluting the global namespace.

However one of Github's additions stuck out at me "env-production".
I have to imagine that `Kyle Neath <http://http://warpspire.com/>`_ was the one who added this to the page, and that he did it because 
he wants the site to render differently based on the runtime environment of the sever. 

I thought about the possibilities of this technique and figured out that there are probably a whole host of ways to use these
**design time classes**. The use of which would help ensure a polished final project. Here are just a couple of examples of how you
might use them:

1. If you are using a specific grid layout you could set an image to appear as a `background-image` of the body. Doing this would 
ensure your page conforms the the correct visual spacing and vertical rhythm. I know that "Blueprint CSS" used to have something like
this back in the day. It might look something like this:

.. code-block:: css

  body.env-development {
    background: url('/assets/grid.png') no-repeat scroll top left !important;
  }

2. Often as developers we'll mock in a bit of functionality that the design calls for with the intention of making it work later.
Unfortunately, this can mean that dead links get deployed. Here is how you could use a CSS selector and a design time class to 
color code all the links *without* a href attribute. This example adds a gaudy eye-searing color to all the dead links, to ensure you
fix it before you deploy into production.

.. code-block:: css

  body.env-development {
    a:not([href]) {
      color:#00FF00 !important;
      background-color:#ff00ff !important; 
    }
  }

The best thing about design time classes is that because they are properly scoped to the body they just *disappear* in the production
environment. This means you don't have to worry about them being seen by the end user.

If you are using Rails it's a pretty straight forward process to get these classes into your application. 

.. code-block:: haml

  %body{ :class => "#{app_classes}" }
  
In your application helper you'd add something like this:

.. code-block:: ruby

  def app_classes
    "#{Rails.env} #{params[:controller].gsub('/',' ')} #{params[:action]}"
  end
  
3. Kyle suggested over twitter that another good use is to `change the favicon <https://twitter.com/kneath/status/304706570246299648/photo/1>`_ based on the server environment.

More To Come
~~~~~~~~~~~~

Do you use Design time classes? If so what are they, share them in the comments or as a gist and maybe we can develop a nice resource
of helpful snippets for others.
