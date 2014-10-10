---
title: Learning to Solve Problems Functionally with Project Euler
layout: default
---

A few of us on the Pittsburgh Learn You a Haskell group are going through the Project Euler problem set in Haskell.  Franklin's been kind enough to set up a template for this in a more official capacity (with a test harness, making it executable, etc.), but for my first problem, I was more interested in the paradigm itself.  This really showed the power of doing things through the REPL, and once I had the solution figured out, I could pretty it up and move it into functions.

I've already done some Project Euler problems in Python, and I wanted to try hitting a new one, so I went with the first problem I hadn't yet solved, problem 9.  Here it is:

```
A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,

a2 + b2 = c2
For example, 32 + 42 = 9 + 16 = 25 = 52.

There exists exactly one Pythagorean triplet for which a + b + c = 1000.
Find the product abc.
```

My first thought was that this a list comprehension problem (remember me talking about list comprehensions in my first Haskell post?  They do come in handy!).  Since the absolute max that any value a, b, or c could be was 1000, I made a list containing all of the right triangles which had a sum of 1000, then took the first .  This is what it looks like in the REPL.

```
*Main> take 1 $ [ (a,b,c) | c <- [1..1000], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2, a + b + c == 1000]
[(200,375,425)]
*Main> 200 * 375 * 425
31875000
```

Of course, c could be an infinite list using [1..], since Haskell would lazily evaluate it and stop once it was able to “take” one element from it.  This was also a really hacky way of doing it.  What I wanted was to get that tuple out of that single-element list, then multiply-fold it.  I could easily get it out of the list with head.  That, however, left me with a 3-tuple.  You can only fold lists, not tuples!  What I wanted to do was something like:

```
foldr (*) (200,375,425)
```

I googled around but couldn't figure out a way to automatically convert a tuple to a list.  I ended up making fst and snd equivalents that work on a 3-tuple, then making a special function to “fold” them by multiplying them:

```
first(x,_,_) = x
second(_,x,_) = x
third(_,_,x) = x

foldMult3Tuple (a,b,c) = first (a,b,c) * second (a,b,c) * third (a,b,c)
```

Finally, the original list comprehension was modified (and the benefits of laziness were added in the form a being an infinite list):

```
findPythTripletSum s = foldMult3Tuple $ head $ take 1 $ [ (a,b,c) | c <- [1..], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2, a + b + c == s]
```

“$” is a really neat function: it basically functions as a left-paren with an invisible right-paren at the end.  In other words, f (g(x)) is the same as f $ g(x).   You can also view it as a pipe in Unix, albeit working from right to left rather than left to right.  This led me to another a-ha moment.

The $ made me think of something my Software Architectures class taught me about pipe and filter systems - that you could think of pipes as just the parentheses in a giant function.  By further analogy, a Unix command with a bunch of piped commands could be thought of  as a functional program – cat foo | uniq | sort can be thought of as sort(uniq(cat(foo))), or in pseudo-Haskell sort $ uniq $ cat foo.  I already knew about functional programming, just in a different way!

I sent out an email to the Pittsburgh Learn You a Haskell mailing list, and Franklin had some improvements for me.  One, instead of making first, second, and third functions specifically for 3-tuples, I could just do this:

```
foldMult3Tuple (a, b, c) = a*b*c
```

I was so intent on re-making fst and snd in my own image that I didn't realize you could do this.  His second suggestion is something I feel kind of embarassed about, actually: doing a “take 1″ and then a “head” is the same thing as doing a “head” by itself.  This makes perfect sense in retrospect, but at the time, I was thinking I needed to make sure that the infinite loop stopped by using take.  This is ridiculous because the language knows that “head” also just gets one thing so it will also stop the infinite list from generating.

The algorithm is also suboptimal.  It's basically, as Franklin noted, just re-stating the problem.  It also does no error-checking, although that is something I actually avoided at first to try to get an answer.  This will probably come back to haunt me at some point, so I'm working on implementing it ASAP before moving on to the next problem.  That will also help me understand the whole Maybe concept a bit better.  I understand the basic idea, but not the details, so that will probably be my next post.

If you're interested in Franklin's (much better) solution to this problem, please see his answer on his Github at: [https://github.com/FranklinChen/project-euler-haskell/blob/master/Euler/Problem9.hs](https://github.com/FranklinChen/project-euler-haskell/blob/master/Euler/Problem9.hs)