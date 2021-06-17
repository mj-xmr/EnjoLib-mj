# EnjoLib
Basic thin C++ libraries with focus on utility, portability, short compilation time and backward compatibility.

Static polymorphism is prefered over template-based polymorphism, mainly due to the compilation time burden, that the template solutions typicaly impose (although they don't always have to: see the `Template/SafePtr*.hpp` templates).

## A little bit or history
The library had been originally written as a common part of [my modules](http://enderspace.de/projects/programs_en.php), extending the functionality of [Orbiter Spaceflight Simulator](http://orbit.medphys.ucl.ac.uk/), and was originally developed [here](http://sf.net/p/enjomitchsorbit). In the following years, I found other mad uses of the library, so I extracted it from there. Occasionally I will make efforts of retaining the backward compatibility with the previous repository and backport the new features from this repo.  

# Licensing
Copyright: Szymon Ender and other contributors (see individual files)
License: **mostly** BSD. See below:

Although the majority of the project is BSD-licensed, some specific parts are GPL-licensed. Please take note of the copyright notices of the headers of the classes, that you'd like to reuse in other projects. In near future, the copyleft GPL code shall be extracted into a seperate library, which will be GPL-only. Currently the classes awaiting for this extraction are:
- Prof. Buckard's numerical classes under `lib/Math/Burkardt`
- `GreatCircleMath` (TODO: point directly to them)
- (TODO: Other?)
Unfortunatly it's not as easy as just moving them outside of the current directory.

There are liberal alternatives to Prof. Buckard's algos, which are still of log(n) complexity, while his are of sub-log(n) complexity. The only algo, that has no alternatives for now is Nelder-Mead, but it can be achieved simpler by looping all the dimensions independently in a loop.

# Author's philosophy 
I'm a passionate programmer, focusing on efficiency of work (better do less but do it well) and clean solutions (anything above Assembly level is meant to be written **for humans**). 
Following these values is able to create wonderful feedback loops in your mind, able to heal the damage, that the IT industry inflicts on you daily.

I have experience with `Java`, `C#` and `Python` as well. I like to take the best out of each language. Ultimately, what matters is not what specific syntax sugar of a given language you use, but the proper and readable design. After all, you want to be able to use a given algo in other languages as well, after just a quick adaptation.

I like to optimize certain parts of the code for runtime speed, but not until these parts are proven to be bottlenecks through measurement tools like [Valgrind](https://valgrind.org/) or even [Profiling by Pausing](https://stackoverflow.com/questions/266373/one-could-use-a-profiler-but-why-not-just-halt-the-program), if no profiling tools are available. The motto I follow here is: "*Don't optimize yet*", coined by [John Bentley](https://medium.com/@sschillace/coding-principles-every-engineer-should-know-b946b48cc946). I hate the dominating dogma in the IT sector ("*I just know this is a bottleneck, because I read this in a book of XYZ Guru.*"). Many "proofs" of such claims are prepared only to proove the claim itself in a perfect isolation and are, strictly speaking, detached from any reality. 

Here's a real life example: A dogmatic person argues, that since inlining functions in his "proof" example, where the functions are tiny and called million of times, leads to repeatable and measurable tripling of the execution speed, now **all** the small functions need to be inlined, even if inlining them leads to additional header inclusions inside the current header. 
The rest of the dogmatic and passive team immediately concurs, inline every small function, leading to a massive increase in compilation time, created by cascaded inclusion of headers, ultimately bringing the project to a halt. Oh. I almost forgot to add. The promised 3x overall runtime speedup was never achieved with the inlining technique for the reason, that even though the small (in terms of lines of code, not the included header tree) functions were indeed being called 3x faster, all of these functions in question **in the real application** attributed for only 1% of the **total** runtime cost. 
Therefore the whole application now runs by whooping 0.67% times faster. I'm so sorry for bursting your bubble, kid. Even an extremely short `Valgrind` session would show you this relationship, before you even started your death march.

For me, what counts is observation/measurement, modeling, testability, reproducibility and finally generalization once it becomes possible, thus extraction of the working idea into a form of an abstract and reusable module. Only this way you may grow, rather than keep moving in circles (unless that's exactly what you're being paid for, eh?).

Regarding the newschool and trending project management methods, "Agile" for me means "flexible" by **all** means and not just by being able to move around in yet another rusty cage, but of a different colour. 
The industry's take of it is that "Agile" quickly regressed into "Waterfall with even more micromanagement and artificial deadlines, cuz these smarta$$es can't be trusted.", which through prolonged demotivation of the most important actors, the devs, ultimately leads projects to collapse, until only the new business cycle is able to refubrish them. And this can happen only **if** the customer is still naive enough to believe in the supplier's sugar coated lies.

Well, that's all about me! 

If, after reading my exhale of arrogance, you still haven't smashed the screen in rage to pieces, then you've just passed the test :) Welcome and please use the code freely in accordance to the licenses. If you'd like to give something back, then roll up your sleeves and please read [CONTRIBUTING](docs/CONTRIBUTING.md). Don't worry - I don't bite. I only bite the hipocritic industry back :)

# Contributions
Contributions are very welcome and most will be merged, as long as the basic principles are adhered to:
- No inclusions of Standard C++ Library's headers inside headers of this library. The STD Lib C++ may be used but only through their inclusion in your .cpp files. Exception: Utility template classes, designed to work directly on STD Lib types, like std::vector. Rationale: Long compilation times of C++ projects result from cascaded inclusions of heavy headers (like Boost) and multiple medium-heavy headers (like STD lib). If you have a great idea to contribute, but it would violate this rule, please consult it first by opening an issue. Maybe it can be still merged, when it's isolated enough. There are such rare cases in this library, where the class' utility has won, because it didn't sacrifice the overall compilation time, thanks to proper isolation (i.e. they are utility classes, rather than basic types).
- Up to C++14, preferrably C++11 standard. Use C++03, whenever possible, although the C++03 is a very soft requirement, which I may later achieve selectively by myself. If you've got a kicka$$ code to contribute, that goes beyond C++14, it must be placed in a subproject (which I will setup TODO). The higher C++ standard project must not be a dependency of this, C++14-standard project (if it's not obvious for you, that is).
- All code, that depends on heavy 3rd party dependencies, such as Boost, must be placed in separate subprojects, in this example: EnjoLibBoost, or EnjoLib3rd (for now) for all other 3rd party deps. The same header rule applies as with the first rule: no inclusions of 3rd party headers in our headers.
- New functionality -> new Unit Tests please. At least some stubs thereof. The tests serve a kind of requirements engineering, therefore require just a little bit of documentation as well.
- Any bugfix of a newly discovered bug **MUST** be accompanied with a reproducible regression test in a form of a Unit Test(s). If we don't stick to this rule, then we **loose a valid test case** and risk reintroducing the bug in future. If you can't write such test by yourself, then we'll try to do it together before merging. Note, that you can't predict all the possible corner cases while writing the code, even using well documented industry methods like [ECP](https://en.m.wikipedia.org/wiki/Equivalence_partitioning) and [BVA](https://en.m.wikipedia.org/wiki/Boundary-value_analysis). I know and used these methods and it's all a utopian waste of time for me in the end, only useful if you belong to the camp of people, who want to write (or are paid for writing) just too many tests, which are in reality equivallent. OTOH, one methodology, that's very close to my heart is [TDD](https://en.wikipedia.org/wiki/Test-driven_development). The TDD however assumes short compilation and linkage times. Since this is extremely rare for a typical C++ project, in practice you won't find many C++ devs, who practice TDD. Here's your chance to learn and have fun with it.
- Tests shall not overfit the implementation. Keep in mind, that there may always be other implementations of the same algo available later, which compute the same thing eihter faster (x)or more precise or even both with a lot of risky optimisations, unlike the reference implementation. The majority of tests should not be able to distinguish between the given algo's various implementations. This allows the same test to cross check the (riskly) optimised versions against the reference implementations under the exactly same conditions.
- When designing classes, please follow the [SOLID](https://en.wikipedia.org/wiki/SOLID) principles.
- Please document the intention of the code you write in the .cpp files in Doxygen syntax for the signatures, as well as please use single/double line comments of the implementation, especially for relatively odd solutions. By doing so, you show a lot of respect to other people, even if it doesn't appear so.
- Don't take reviews personally nor emotionally :) This is all meant to be productive and fun at the same time. 
- The new code shall have a BSD-licence (or compatible) clause at the **end** of each file. Any copyleft code shall go to the GPL submodule (TODO: once the module is ready. For now, a clear copyright notice is enough).
- The PRs should be transactional and small enough. Fixing unrelated code smells is welcome, but ideally only in the files being touched in given PR. And don't forget to make sure, that the fix you do is unit tested.
- Don't be shy of any contributiuons. Anything that's somehow not fitting, will be addressed during a review anyway. 
- I might tip you for great contributions, but I can't promise this at all times, so don't feel undervalued, if I don't do it. Sometimes I may be simply outta loot.
