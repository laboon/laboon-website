---
title: Ruby HoodPop and RubyClean - Two Static Analysis Tools for Ruby
layout: default
---

While the Ruby culture seems to really celebrate dynamic testing, there is less emphasis on static analysis tools.  While there are certainly good examples of static analysis tools for Ruby such as flog, they're just not as popular as statically-typed languages like Java.  Now part of this is because doing static analysis on a dynamic language is much more difficult.  However, that's no reason not to try!  With that in mind, I have put together two small scripts for helping with static analysis of your Ruby code.

The first, HoodPop, allows you to see how Ruby tokenizes, parses, and compiles your code.  This will probably only be of interest for the die-hard performance nuts out there, most of whom are probably coding in C anyways.  It can be interesting to see, however, and will definitely help you understand what Ruby is doing with your code “under the hood.”  I wrote this tool as part of a Lightning Talk I gave at Pittsburgh Ruby in February, if you'd like to see the slides or video of my “Ruby Under the Hood” talk.  To run it, just give it the name of a Ruby (.rb) file as an argument.

Code: [https://github.com/laboon/hoodpop](https://github.com/laboon/hoodpop "HoodPop")

The second is arguably more useful.  When I review people's code (and when my code is reviewed), one of the most common issues I see is people forgetting to remove their old commented-out code.  There's no real need to keep most of it around when you have version control, and almost every time it's just an oversight.  Therefore, I wrote a simple script which will check for potentially commented-out code in a codebase.  It's not fool-proof, but I tried to err on the side of showing you more than less – you can always grep -v some lines out from the output if, for instance, your comments contain an abundance of curly braces.  To run this, just give it a list of root directories as arguments.  It will only look at .rb files, but you could easily extend it for .erb and other files.  You'd probably want to change the regexes around in the possibly_code? function first, but it should be relatively self-explanatory.

Code: [https://github.com/laboon/RubyClean](https://github.com/laboon/hoodpop "RubyClean")