So you've heard some rumor and hearsay about asm.js, but you don't know where to start...

How about a live code example that will run in Firefox 32+. If you use the Firefox homepage when opening a new tab/window, you might have noticed the little voxel dude waiting on a game board. Clicking the "?" icon in the right-hand corner of this board produces the message, "For a short time only, we've put this game on our home page to show off the latest gaming technology in Firefox. Have fun!"

![Firefox_32+](try_asm.js_in_firefox/firefox_home.jpg)

The game, message, and [link](http://mzl.la/1tr8l1i?sample_rate=0.1&snippet_name=4606) allude to the fact that these new Firefox releases include some major [updates to the SpiderMonkey engine](https://wiki.mozilla.org/Javascript:SpiderMonkey:OdinMonkey). This includes the ability to run Ahead-of-Time (asm.js) code. 

But where do you get such code? Well, short of compiling it from another language, you can actually write native JavaScript functions that will validate as asm.js within Firefox. Here's an example from the homepage of the [current working draft](http://asmjs.org/spec/latest/#introduction) :

```
/* From http://asmjs.org/spec/latest/#introduction */
	
	function GeometricMean( stdlib, foreign, buffer ) {
		"use asm";

		var exp = stdlib.Math.exp;
		var log = stdlib.Math.log;

		var values = new stdlib.Float64Array(buffer);

		function logSum(start, end) {
			start = start|0;
			end = end|0;

			var sum = 0.0, p = 0, q = 0;

			for( p = start<<3, q = end<<3; (p|0) < (q|0); p = (p+8)|0 ) {
				sum = sum + +log(values[p>>3]);
			}

			return +sum
		}

		function geometricMean( start, end ) {
			start = start|0;
			end = end|0;

			return +exp(+logSum(start,end) / +((end - start)|0));
		}

		return { geometricMean: geometricMean };
	}

	1;
```

If you simply include this source file in the script tag of a bare html page and load that page in Firefox 32+ (I'm using 32.0.3), the browser console will report:

```Successfully compiled asm.js code (total compilation time 0ms; not stored in cache)```

Notice that this code is only a definition of a function in asm.js format, which is ready to be exported into some actual running program.

Now to reasonably test this function, I did have to find what a geometric mean is because it's apparently not the same as an average (or arithmetic mean). Where as an average operates on the sum of a set of numbers, the geometric mean takes the [*n*th root of the product of *n* numbers](http://en.wikipedia.org/wiki/Geometric_mean). Good to know:
```
```
