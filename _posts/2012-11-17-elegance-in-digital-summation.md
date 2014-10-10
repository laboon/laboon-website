---
title: Elegance in Digit Summation
layout: default
---

One of the things that I've noticed about functional programming, and Haskell in particular, is that it really makes you think about the problem in order to get a good solution.  Programming in general is often punctuated with long stretches of what seem to be wasted brain cycles with brief flashes of inspiration.  Functional programming seems to be this to the nth power, but those brief flashes lead to incredibly elegant code.  Those brief flashes are also becoming more and more regular as I get used to the functional way of thinking.

Tonight, I was working on Problem 16 of Project Euler:

2^15 = 32768 and the sum of its digits is 3 + 2 + 7 + 6 + 8 = 26.

What is the sum of the digits of the number 2^1000?

Solving this involved only three lines of code – and two of them are descriptive, as opposed to actual execution steps.

So… where to start?  I knew that I was going to need to turn the value of 2^1000 into a String (which is really just a list of Chars), and then convert each of those individual chars into an integer (e.g., “1″ becomes 1, “2″ becomes 2, etc.).  Once I had that, I knew it would be a relatively simple process to sum everything up – this is what the fold commands are made for.

My first question was how to convert a number into a string.  A quick bit of googling led me to show.  That's a pretty useful command – it takes any type that can be shown and turns it into a string (which in Haskell is just a list of chars).

```haskell
*Main Data.Char> :t show
show :: Show a => a -> String
```

Now I have converted 2^1000 into “1071508607186267320948425049060001810... (lots of digits) ...37205668069376″.  Technically, of course, this is ['1', '0', '7'... '6'].  The next step was to convert each of these characters into an integer instead of a character which represented an integer.  Again, very simple – the map command was made for such cases.  That raised the question, however – what function do I need to pass in to the map command?  I tried everything I could think of – read, fromEnum, fromIntegral – before finally giving up and Googling it.

Google wasn't of much help – but Hoogle was.  If you've never used Hoogle, I highly recommend it.  It's a way to search Haskell functions by the types that they accept and output.  I knew I needed to take a Char and return an Int, so I searched for:

```
Char -> Int
```

and the digitToInt command was the first result!  I would need to import Data.Char to use it, but I like this method of finding functions more than searching through the massive Java API.  I imported Data.Char and went on my merry way.

Now I had a list of integers – [1,0,7... 6].  It was absolutely trivial to fold them together via addition, then add the function definition (which Haskell will, of course, do for you if you don't want to).  This led me to the final three-line program, with only one line of actual execution.

```
import Data.Char

digitSum :: Show a => a -> Int
digitSum n = foldr (+) 0 (map (digitToInt) (show n))
```

One line to sum up all the digits in an arbitrary-length number. Now that is elegant.