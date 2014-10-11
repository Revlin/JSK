If you'd like to get started with [Karma](http://karma-runner.github.io/), the multi-browser, "spectacular test runner for JavaScript", and you happen to be on Windows, get ready to rumble with karma.conf.js

Most of the documentation on Karma, including the official docs, ask you to use the CLI command ```karma init``` with an optional filename tacked on to the end. This command takes you through a series of questions that are supposed to auto-magically build the 'karma.conf.js' file (if you didn't include a filename). This magical file will supposedly instruct Karma on how to conduct it's business when you run ```karma start```. However, I have spent not so few hours searching, reading and implementing fixes for the troublesome conflicts in this little file. 

Pretty much, no matter what changes I make, this is the console's response to running ```karma start [my.conf.js]``` on a Windows 8 workstation:
```
INFO [karma]: Karma v0.12.24 server started at http://localhost:9876/
INFO [launcher]: Starting browser Firefox
WARN [web-server]: 404: [...]\JSK\node_modules\karma\static/karma.js
WARN [web-server]: 404: [...]\JSK\node_modules\karma\static/karma.js
```

Googling "Karma WARN [web-server]: 404" yeilds very few useful results, even if one appends "+karma.js" (the missing file) onto that query. This [github issue](https://github.com/karma-runner/karma/issues/554) seemed helpful, though:

> bruno-c commented on May 22, 2013
>
> _Actually something that I found confusing is that if 
> you read the documentation for the configuration file 
> straight from [here](https://github.com/karma-runner/karma/issues/554), it links to the master branch 
> version of karma.conf.js which uses the new module format. 
> But if you install Karma from NPM, you're getting the 
> stable branch so that config file won't work._

Ah, so now I know there are two different configuration file formats for Karma, an old and a new. So, which is which? I decide to try the format listed on Karma's [official website](http://karma-runner.github.io/0.8/config/files.html), which goes as follows for the ```file``` key:
```
files = [

  // Adapter
  JASMINE,
  JASMINE_ADAPTER,

  // simple patterns to load the needed testfiles
  'test/fixtures/**/*.html',
  'test/unit/*.spec.js',

  // this file gets served but will be ignored by the watcher
  {pattern: 'compiled/index.html', watched: false},

  // this file only gets watched but otherwise ignored
  {pattern: 'app/index.html', included: false, served: false}
];
```

This leads to a bunch of errors and an ugly stack dump in my console. Unfortunately, if you follow the link a above you might notice that one part of the path is '0.8' as in Karma v0.8. I didn't notice this until after Karma had taken a dump. Darn those expired links! So *this* is the old config file format. Well then, back to the [new](http://karma-runner.github.io/0.12/config/files.html):  
```
frameworks: ['jasmine'],
files: [

  // simple pattern to load the needed testfiles
  // equal to {pattern: 'test/unit/*.spec.js', watched: true, served: true, included: true}
  'test/unit/*.spec.js',

  // this file gets served but will be ignored by the watcher
  // note if html2js preprocessor is active, reference as `window.__html__['compiled/index.html']`
  {pattern: 'compiled/index.html', watched: false},

  // this file only gets watched and is otherwise ignored
  {pattern: 'app/index.html', included: false, served: false}
],
```

*And* I'm back where I started:
```
WARN [web-server]: 404: [...]\JSK\node_modules\karma\static/karma.js
```

