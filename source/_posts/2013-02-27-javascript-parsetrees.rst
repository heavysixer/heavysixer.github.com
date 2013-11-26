---
layout: post
title: "Javascript ParseTrees"
date: 2013-02-27 20:22
comments: true
categories: [Ruby, JavaScript]
---
I've been experimenting with the rkelly Ruby gem to help me explore the JavaScript parse tree. It is really fascinating, and I can see
myself spending a lot of time spelunking through the language. Here is a simple example using the gem to iterate over each node in the
parse tree and print out its type. Stay tuned, more to come!

.. code-block:: ruby

  require 'rubygems'
  require 'rkelly'
  parser = RKelly::Parser.new
  src = <<EOF
  // Create scrollLeft and scrollTop methods
  jQuery.each( {scrollLeft: "pageXOffset", scrollTop: "pageYOffset"}, function( method, prop ) {
    var top = "pageYOffset" === prop;

    jQuery.fn[ method ] = function( val ) {
      return jQuery.access( this, function( elem, method, val ) {
        var win = getWindow( elem );

        if ( val === undefined ) {
          return win ? win[ prop ] : elem[ method ];
        }

        if ( win ) {
          win.scrollTo(
            !top ? val : window.pageXOffset,
            top ? val : window.pageYOffset
          );

        } else {
          elem[ method ] = val;
        }
      }, method, val, arguments.length, null );
    };
  });

  function getWindow( elem ) {
    return jQuery.isWindow( elem ) ? elem : elem.nodeType === 9 && elem.defaultView;
  }
  EOF
  ast = parser.parse(src)

  =begin
  Outputs something like this as it traverses the parseTree
  RKelly::Nodes::SourceElementsNode
  RKelly::Nodes::ExpressionStatementNode
  RKelly::Nodes::FunctionCallNode
  RKelly::Nodes::DotAccessorNode
  RKelly::Nodes::ResolveNode
  RKelly::Nodes::ArgumentsNode
  RKelly::Nodes::ObjectLiteralNode
  RKelly::Nodes::PropertyNode
  ...
  =end

  ast.each do |node|
    puts node.class
  end 