If you'd like to get started with [Karma](http://karma-runner.github.io/), the multi-browser, "spectacular test runner for JavaScript", and you happen to be on Windows, get ready to rumble with karma.conf.js

![Karma](http://karma-runner.github.io/assets/img/banner.png)

Most of the documentation on Karma, including the official docs, ask you to use the CLI command ```karma init``` with an optional filename tacked on to the end. This command takes you through a series of questions that are supposed to auto-magically build the 'karma.conf.js' file (if you didn't include a filename). This magical file will supposedly instruct Karma on how to conduct it's business when you run ```karma start```. However, I have spent not so few hours searching, reading and implementing fixes for the troublesome conflicts in this little file. 

Pretty much, no matter what changes I make, this is the console's response to running ```karma start [my.conf.js]``` on a Windows 8 workstation:
```
INFO [karma]: Karma v0.12.24 server started at http://localhost:9876/
INFO [launcher]: Starting browser Firefox
WARN [web-server]: 404: [...]\JSK\node_modules\karma\static/karma.js
WARN [web-server]: 404: [...]\JSK\node_modules\karma\static/karma.js
```

Googling "Karma WARN \[web-server\]: 404" yeilds very few useful results, even if one appends "+karma.js" (the missing file) onto that query. This [github issue](https://github.com/karma-runner/karma/issues/554) seemed helpful, though:

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

I guess it's time to take a big step back. Maybe the quick and easy "recommended approach" to install Karma is just not going to work for me. How about approaching this the way a developer would, if she had was planning on [making changes](http://karma-runner.github.io/0.12/dev/making-changes.html) to the Karma source:
```
C:\...\lib> git clone https://github.com/karma-runner/karma.git
C:\...\lib> cd karma
C:\...\karma> git fetch origin --tags +refs/tags/*:refs/rtags/remote/*
C:\...\karma> git checkout tags/v0.12.24
C:\...\karma> git checkout -b release/v0.12.24
```

At this point I have a fresh pull of the same source code that was released as version 0.12.24 and I have created a new branch to work in, called 'release/v0.12.24'
```
C:\...\karma> node scripts/init-dev-env.js
```

From here alot of stuff (good stuff?) starts to print, ending with:
```
YAY, YOUR WORKSPACE IS READY!
```

Yeah, I think that's good. Time to run some tests, but this time I will be testing Karma itself via the included grunt files:
```
C:\...\karma> grunt test
...
...
...
INFO [launcher]: Trying to start Chrome again (2/2).
WARN [web-server]: 404: [...]\JSK\lib\karma\static/karma.js
WARN [launcher]: Chrome have not captured in 20000 ms, killing.
ERROR [launcher]: Chrome failed 2 times (timeout). Giving up.
[Error]
Fatal error: Client unit tests failed.

```

Not quite back where I started, but maybe I'm getting closer to explicating the failing factor? Oddly dumping most of the ERROR message into the google search lead to another [useful comment](https://github.com/karma-runner/karma/issues/1028) on github:

> DanTup commented on Apr 12
>
> _Yep; that PR fixes all failing 7 of the failing tests I posted :thumbsup:
> However, the subsequent tests now fail (Chrome opens; but doesn't seem to 
> connect/load the page). This probably happened before too, it just didn't 
> get this far. It might be a local setup issue; not done much digging yet:_
> ```
> INFO [karma]: Karma v0.11.14 server started at http://localhost:9876/
> INFO [launcher]: Starting browser Chrome
> WARN [web-server]: 404: M:\Coding\TestApps\karma2\static/karma.js
> WARN [launcher]: Chrome have not captured in 20000 ms, killing.
> INFO [launcher]: Trying to start Chrome again (1/2).
> WARN [web-server]: 404: M:\Coding\TestApps\karma2\static/karma.js
> WARN [launcher]: Chrome have not captured in 20000 ms, killing.
> INFO [launcher]: Trying to start Chrome again (2/2).
> WARN [web-server]: 404: M:\Coding\TestApps\karma2\static/karma.js
> WARN [launcher]: Chrome have not captured in 20000 ms, killing.
> ERROR [launcher]: Chrome failed 2 times (timeout). Giving up.
> ```
>
> deepak1556 commented on Apr 12
>
> _cool, once everything works fine we shld have tat PR updated and merged, Thanks!_
>
>
> sylvain-hamel commented on Apr 12
>
> _the 404 might be because you have not built the project using grunt build_
>
>
> DanTup commented on Apr 12
>
> _Oops, @sylvain-hamel is correct; I didn't build when I cloned the other repo!_
>

DanTup seems to have encountered the same error I am getting, so let me try sylvain-hamel's suggestion:
```
C:\...\karma> grunt build
Running "browserify:client" (browserify) task

Done, without errors.
```

Wow, that was short and sweet. And the client test?
```
C:\...\karma> grunt test:client
INFO [karma]: Karma v0.12.24 server started at http://localhost:9876/
INFO [launcher]: Starting browser Chrome
INFO [Chrome 39.0.2138 (Windows 8)]: Connected on socket rjcQ459piu_P2KBjQo1E wi
th id 92813343
.........................
Chrome 39.0.2138 (Windows 8): Executed 25 of 25 SUCCESS (0.024 secs / 0.018 secs
)

Done, without errors.

```

Booyah! 

I now presume that this Karma build can be installed and will succeed in testing my project files...
```
C:\...\karma> cd ..\..
C:\...\JSK> npm install lib\karma
C:\...\JSK> node node_modules\karma\bin\karma init

Which testing framework do you want to use ?
Press tab to list possible options. Enter to move to the next question.
> jasmine

Do you want to use Require.js ?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture any browsers automatically ?
Press tab to list possible options. Enter empty string to move to the next quest
ion.
> Chrome
>

What is the location of your source and test files ?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Enter empty string to move to the next question.
> js/*.js
> tests/*.js
>

Should any of the files included by the previous patterns be excluded ?
You can use glob patterns, eg. "**/*.swp".
Enter empty string to move to the next question.
> karma.conf.js
>

Do you want Karma to watch all the files and run the tests on change ?
Press tab to list possible options.
> yes

Config file generated at "[...]\JSK\karma.conf.js".
```

Notice that I've entered "js/\*.js" and "tests/\*.js" as patterns for finding my source and test code, respectively. The first directory contains a JavaScript file which defines my class/object. The second direcotry contains a JavaScript file which tests this same class/object following a Jasmine style spec. Now it's time to test:

```
C:\...\JSK> node node_modules\karma\bin\karma start
INFO [karma]: Karma v0.12.24 server started at http://localhost:9876/
INFO [launcher]: Starting browser Chrome
INFO [Chrome 39.0.2138 (Windows 8)]: Connected on socket TfBE-RIFVkn6IxaqU_nw wi
th id 76067694
Chrome 39.0.2138 (Windows 8): Executed 2 of 2 SUCCESS (0.01 secs / 0.008 secs)
```
*That's* what I'm talking (writing) about!
