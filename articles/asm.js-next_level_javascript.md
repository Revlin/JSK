JavaScript is reaching all new programming language heights via the introduction of "an extraordinarily optimizable, low-level subset" called [asm.js](http://asmjs.org/).

How does it work? Well, the would-be asm.js developer begins with a statically-typed language (C, anybody?) and uses a source-to-source compiler to translate that low-level code into JavaScript. Actually, this places the original code into a kind of memory-leak-safe sandbox which is implemented in JavaScript. The idea behind why this makes the resulting JavaScript fast is that "[it is intended to have performance characteristics closer to that of native code than standard JavaScript by limiting language features to those amenable to ahead-of-time optimization and other performance improvements](http://en.wikipedia.org/wiki/Asm.js)."

![Epic Citadel](http://upload.wikimedia.org/wikipedia/en/9/9c/Epic_Citadel_Screenshot.jpg)

[Epic Citadel](https://www.unrealengine.com/showcase/epic-citadel), an Unreal Engine 3 boasting-parade, was ported to asm.js and subsequently clocked at 60 FPS on the latest browsers. However, these benchmarks are tied directly to the hardware running the browser, just like benchmarks of conventional desktop apps. Which is pretty much the point: asm.js allows high-performance apps to be developed for the browser, which are expected to run without plugins at the same speeds as their desktop equivalents.

The specification for asm.js is still a working draft, the latest of which can be [read here](http://asmjs.org/spec/latest/).
