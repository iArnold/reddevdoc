https://github.com/red/red/wiki/Question-about-contributing-to-core-parts-of-Red%27s-toolchain

Question about contributing to core parts of Red's toolchain
============================================================

Questions originate from the [Red Development Team](http://chat.stackoverflow.com/transcript/message/15637085#15637085) group on StackOverflow.

<u>Richard</u>: _I know there are plans to rewrite R/S in Red later, but how open, in general, are you to improvements to R/S at the moment? I'd really like to make a stab at different sized integer types and also by value structs and arrays for instance. Having said that, I'm promising anything either; my endless free time is curiously limited. :)_

_Also generally speaking, I think that some possible improvements could need an additional compiler pass. Would that be ok, or is it something you really want to avoid?_
_That should have been: "I'm not promising anything either ..." :)_

_Just to be clear: I'm just asking how likely you are to accept rewrites of parts of the R/S compiler, like the type system code for instance._

_Of course specific R/S language and type changes need to be discussed. I do think improving R/S even before Red is done would be beneficial for both languages._

***

<u>Nenad</u>: Red and Red/System toolchain (compilers/linker) are bootstrapped with Rebol2 until we can rewrite them using Red. The goal from the beginning is to reach the selfhosted stage that will enable JIT-compilation of Red and Red/System code, giving us access to the complete range of planned Red semantics.

The architecture of the selfhosted Red compilers/linker will be very different from the current one. The compilers and linkers will have pipelined stages with a public API. The core will be just an empty shell, all the features will be implemented as plugins using the public API. The native compiler will feature an IR (Intermediary Representation) that will enable sophisticated optimizations. Optimizations will be also achieved through specific plugins that could be enabled/disabled depending on the requirements (AOT vs JIT, fastest possible, smallest possible,...). The selfhosted architecture will be *very* suitable for third-parties contribution, due to the very loose coupling involved.

In consequence, all the current toolchain written in Rebol2 (Red and Red/System compilers, the linker) will be *trashed* after 1.0 for 2.0 to come to life. So, the architecture of the current code has been simplified drastically to implement it as fast as possible. The current architecture is monolithic, and not much suited for extensions or refactoring. Investing more in it now is basically a waste of time, as nor the architecture, nor the source code will be kept in 2.0. So, wrt optimizing layers for example, they  are all deferred to the selfhosted version, in order to not waste precious coding time before reaching 1.0.

That said, as we have to live with the current codebase for still some time, there are improvements that can be done and that are desirable, we just need to ensure that we spend the less possible amount of time on such tasks. The criterion for accepting changes is simple: are the changes required for building the planned Red 1.0 features? If the answer is yes, then the changes should be considered.

For example, support for int16! would be nice as the Unicode handling relies partially on UCS-2 support, which requires 16-bit accesses. Having a 16-bit datatype would simplify and make more robust the existing UCS-2 handling code. Some bindings with existing OS features also would benefit from a native 16-bit datatype.

A 64-bit integer is currently not required by the Red runtime, and if the need for it doesn't arise in the roadmap for 1.0, it would fall into the "nice to have" category (as opposed to "must to have"). Though, internal construction needs aside, we also have "marketing" considerations, and probably users are nowadays expecting to have default support for a 64-bit integer type in a new programming language, so this is something that also needs to be seriously taken into account. So the final answer is: we most probably will need a 64-bit integer datatype for 1.0. :-)

About the addtional compiler pass, I am quite reluctant for that if that means a lot of additional code and slower performances. I would like to see the current compilation performances improve, not decrease. Red compilation times are currently really bad compared to the goal I have: less than a second for one page of Red code, on an average hardware. There's an item in our roadmap to 1.0 to address that.

Another important point to me about accepting significant changes and third-party amount of code in the compiler's core parts, is that I need to be able to debug any issue in the toolchain, at any level. If I'm not comfortable with the third-party code parts, it will take me much more time to debug issues and that will finally translates to greater risk of missing the major releases deadlines and postpone probably 1.0. So, this is something that I take seriously in account when considering a big pull request on the core parts.

So, to sum up things, each new feature proposition or deep change needs to be discussed and decisions will be taken on a case by case basis, mainly weighting with the above considerations.

Hope this does not chill you much, there are strong limitations we need to take into account on the road to 1.0, if we want to reach it in an acceptable timeframe, so we can all have much more fun working on 2.0. ;-)
