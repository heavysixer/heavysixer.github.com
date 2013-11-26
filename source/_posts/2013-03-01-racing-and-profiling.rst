---
layout: post
title: "racing and profiling"
date: 2013-03-01 09:08
comments: true
categories: [JavaScript]
---
I've been experimenting with various ways to profile, and explore JavaScript as it executes in the runtime environment. Mostly I've been 
experimenting with the rKelly and rubyracer gems. Both gems are written by people much smarter than myself so there is lots to learn and
explore inside their source. I was talking to the very friendly `Charles Lowell <https://twitter.com/cowboyd>`_, creator of the rubyracer 
and he shared this great snippet with me, which allows you to turn on the v8 profiler while the rubyracer is running. Because this is an 
undocumented hook I thought I'd share it here:

.. code-block:: ruby

  ruby -Ilib -Iext -rv8 -e 'V8::C::V8::SetFlagsFromString("--prof"); V8::Context.new() {|c| puts c.eval("5 + 1")}; V8::C::V8::PauseProfiler()'

This will produce a *v8.log* file wherever you executed the script from. Inside the file there is a gluttonous amount of data, which will 
take some time to parse through but in general it looks a bit like this:


::
  
  code-creation,LoadIC,0x127fc3e29140,181,"A load IC from the snapshot"
  code-creation,KeyedLoadIC,0x127fc3e29200,181,"A keyed load IC from the snapshot"
  code-creation,StoreIC,0x127fc3e292c0,183,"A store IC from the snapshot"
  code-creation,KeyedStoreIC,0x127fc3e29380,183,"A keyed store IC from the snapshot"
  code-creation,Builtin,0x127fc3e29440,97,"A builtin from the snapshot"
  code-creation,Builtin,0x127fc3e294c0,137,"A builtin from the snapshot"
  code-creation,Script,0x127fc3e14e20,980,"native string.js",0x2e87cc50ec50,
  code-creation,LazyCompile,0x127fc3e15500,1616,"SetUpString native string.js:940",0x2e87cc5129c8,
  code-creation,LazyCompile,0x127fc3e15be0,472," native string.js:36",0x2e87cc512ab0,
  code-creation,Script,0x127fc3e15dc0,336,"native array.js",0x2e87cc512e00,
  code-creation,LazyCompile,0x127fc3e15f20,2544,"SetUpArray native array.js:1469",0x2e87cc5175b0,
  code-creation,LazyCompile,0x127fc3e16920,340,"SetUpArray.b native array.js:1482",0x2e87cc517668,
  code-creation,Script,0x127fc3e16b00,552,"native regexp.js",0x2e87cc5177f0,
  code-creation,LazyCompile,0x127fc3e16d40,388,"RegExpConstructor native regexp.js:86",0x2e87cc518a70,
  code-creation,LazyCompile,0x127fc3e16ee0,280,"RegExpMakeCaptureGetter native regexp.js:363",0x2e87cc519288,
  code-creation,LazyCompile,0x127fc3e17000,668," native regexp.js:364",0x2e87cc519340,
  code-creation,LazyCompile,0x127fc3e172a0,2304,"SetUpRegExp native regexp.js:403",0x2e87cc519488,
  code-creation,LazyCompile,0x127fc3e17ba0,292,"SetUpRegExp.a native regexp.js:422",0x2e87cc519540,
  code-creation,LazyCompile,0x127fc3e17ce0,256,"SetUpRegExp.c native regexp.js:426",0x2e87cc519658,
