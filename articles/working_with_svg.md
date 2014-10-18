Here's the js fragment I had to add to line 16058 of the un-minified 'angular.js' in order to make native SVG work with angular:
```
	/* REV EDIT:
	* Fix pathname parsing...
	*/
	if( href.match(/^https?:\/\/(?:[^:@\/]+(?::[^@\/]+)?@)?([\w|\-|\.]+)(?::\d+)?(?:\/.*)?$/) !== null ) {
		var parts = href.match(/^https?:\/\/(?:[^:@\/]+(?::[^@\/]+)?@)?([\w|\-|\.]+)(?::\d+)?(\/.*)?$/);
		urlParsingNode.host = parts[1];
		urlParsingNode.pathname = parts[2];
	}
	/* END EDIT */
```

... oh and here's an example:
```
<?xml version="1.0" encoding="utf-8"?>

<svg width="640" height="360" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
	<script type="text/ecmascript" xlink:href="lib/angular/angular.js"></script>
	<script type="text/ecmascript">	
	//<![CDATA[
		var practice = angular.module('practice-angularjs', []);
	//]]>
	</script>
	<style type="text/css">
		text.title {
			fill:		#000;
			stroke:		none;
			font-family:sans-serif;
			font-weight:bold;
			font-size:	34pt;
		}
	</style>
	
	<g data-ng-app="practice-angularjs" 
	   data-ng-init="name = 'World'">
		
		<circle cx="480" cy="90" r="30" />
		
		<text class="title"
			  x="6"
			  y="72"
			  textLegnth="400"
			  lengthAdjust="spacingAndGlyphs">Hello {{name}}!
		</text>
	</g>
	
</svg>
```
