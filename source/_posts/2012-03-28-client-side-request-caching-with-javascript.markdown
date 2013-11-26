---
layout: post
title: "client-side request caching with JavaScript"
date: 2012-03-28 20:30
comments: true
categories: ['JavaScript']
---

Recently I was writing an enterprise data visualization application that made heavy user of interactive charts and graphs. Like most best-of-breed data-viz apps this one supported very robust filters for slicing and dicing through the dataset. Each time the user adjusted one of these filters the application made new AJAX request and idled until the results were returned.

Technically, this approach worked fine, but because the data-segmentation occurred on the server the charts felt sluggish because they were always polling or data. Additionally, the user quite frequently toggled between only a couple filters to compare the results. What should have been an experience of rapidly flipping between two views on the data was actually a belabored rendering experience. As the developer this was frustrating because they were asking for and receiving the same data over and over again.

To solve this problem, I built a very simple mechanism that affords _just enough caching_ to persist these payload objects only while the user is viewing the page. In this way the user would be guaranteed to get a fresh copy from the server on each page load.

Essentially, I hooked my caching routine around the function that made the AJAX request for new chart data. Using this approach an AJAX request only occurred once, and all future requests pulled from the cache.

``` javascript
// Called when someone adjusts a filter
function updateChart(url, chart, key) {

    // Builds the request params needed to correctly query the server. 
    var opts = requestParamsFor(chart, key);

    // Generate a cache key based on this object
    var cacheKey = $.cache.getKey(opts);

    if ($.hh.cache.exists(cacheKey)) {

        // If the key exists then the request has happened in the past
        // use the cached result to refresh the chart.
        var result = $.cache.get(cacheKey);
        onSuccess(kind, opts, chart, code, result);
    } else {
        $.ajax({
            url: url,
            type: 'POST',
            data: opts,
            success: function(result) {

                // Since this was a new request store the results in the cache 
                // at the location specified by the cache key.
                $.cache.add(cacheKey, result);
                onSuccess(kind, opts, chart, code, result);
            }
        });
    }
}

```

Here is the local cache class in all it's detail:

``` javascript
$.cache = (function() {
    var _cache = {};
    var _keys = [];
    var _indexOf = function(arr, obj) {
        var len = arr.length;
        for (var i = 0; i < len; i++) {
            if (arr[i] == obj) {
                return i;
            }
        }
        return - 1;
    };
    var _serialize = function(opts) {
        if ((opts).toString() === "[object Object]") {
            return $.param(opts);
        } else {
            return (opts).toString();
        }
    };
    var _remove = function(key) {
        var t;
        if ((t = _indexOf(_keys, key)) > -1) {
            _keys.splice(t, 1);
            delete _cache[key];
        }
    };
    var _removeAll = function() {
        _cache = {};
        _keys = [];
    };
    var add = function(key, obj) {
        if (_keys.indexOf(key) === -1) {
            _keys.push(key);
        }
        _cache[key] = obj;
        return $.hh.cache.get(key);
    };
    var exists = function(key) {
        return _cache.hasOwnProperty(key);
    };
    var purge = function() {
        if (arguments.length > 0) {
            _remove(arguments[0]);
        } else {
            _removeAll();
        }
        return $.extend(true, {},
        _cache);
    };
    var searchKeys = function(str) {
        var keys = [];
        var rStr;
        rStr = new RegExp('\\b' + str + '\\b', 'i');
        $.each(_keys,
        function(i, e) {
            if (e.match(rStr)) {
                keys.push(e);
            }
        });
        return keys;
    };
    var get = function(key) {
        var val;
        if (_cache[key] !== undefined) {
            if ((_cache[key]).toString() === "[object Object]") {
                val = $.extend(true, {},
                _cache[key]);
            } else {
                val = _cache[key];
            }
        }
        return val;
    };
    var getKey = function(opts) {
        return _serialize(opts);
    };
    var getKeys = function() {
        return _keys;
    };
    return {
        add: add,
        exists: exists,
        purge: purge,
        searchKeys: searchKeys,
        get: get,
        getKey: getKey,
        getKeys: getKeys
    };
}).call(this);
```

Here are some jasmine tests which explain more features of the cache not covered in this post, and prove that it works!

``` javascript
it("should allow you to build a cache using keys",
function() {
    var obj = {
        'foo': 'bar'
    };
    expect($.cache.exists("foo=bar")).toEqual(false);
    expect($.cache.getKey(obj)).toEqual('foo=bar');
    expect($.cache.getKey('foo')).toEqual('foo');
    expect($.cache.add("foo=bar", obj)).toEqual(obj);
    expect($.cache.exists("foo=bar")).toEqual(true);
    expect($.cache.get("foo=bar")).toEqual(obj);
    expect($.cache.get("bar")).toEqual(undefined);
});

it("should allow you to empty the cache completely",
function() {
    $.cache.purge();
    expect($.cache.add("baz", 'baz')).toEqual('baz');
    expect($.cache.getKeys().length).toEqual(1);
    expect($.cache.purge()).toEqual({});
});

it("should allow you to empty the cache of just a specific record",
function() {
    $.cache.purge();
    expect($.cache.add("baz", 'baz')).toEqual('baz');
    expect($.cache.add("boff", 'ball')).toEqual('ball');
    expect($.cache.getKeys()).toEqual(['baz', 'boff']);
    expect($.cache.purge('boff')).toEqual({
        'baz': 'baz'
    });
    expect($.cache.getKeys()).toEqual(['baz']);
    expect($.cache.purge('bozz')).toEqual({
        'baz': 'baz'
    });
    expect($.cache.getKeys()).toEqual(['baz']);
});

it("should allow you to search for keys in the cache",
function() {
    $.cache.purge();
    var obj = {
        'bar': 'baz'
    };
    $.cache.add('bar=baz', obj);
    expect($.cache.getKeys().length).toEqual(1);
    expect($.cache.getKeys()).toEqual(["bar=baz"]);
    expect($.cache.searchKeys("bar")).toEqual(["bar=baz"]);
    expect($.cache.searchKeys("bar=")).toEqual(["bar=baz"]);
    expect($.cache.searchKeys("bat")).toEqual([]);
});
```
