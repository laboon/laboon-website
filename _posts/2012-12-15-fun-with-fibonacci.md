---
title: Fun with Fibonacci, or How to Drive a (Bad) Interviewer Crazy
layout: default
---

Meet Johnny.  Johnny graduated from $UNIVERSITY with a degree in Computer Science, then went to $OTHER_UNIVERSITY to get his Master's in Software Engineering.  Now he's ready to face the real world, and has an interview at $BIG_TECH_COMPANY.  Johnny was a good student, and understands algorithms as well as software engineering principles.  He's as prepared as one can be for his interview.

INTERVIEWER: Hi Johnny.  I thought we'd start off with a simple question.  Do you know about the Fibonacci sequence?

JOHNNY: Of course.  It's the sequence of numbers which start with 0,1,1, and the nth Fibonacci number is the sum of the previous two.

INTERVIEWER: Very good.  Could you tell me how you would code up a function to return the nth Fibonacci number?

JOHNNY: Sure!  Since these have been calculated numerous times, I would probably figure out the maximum Fibonacci number that would ever have to be calculated, and make a lookup table.  Even with arbitrary-precision arithmetic, this would take up a trivial amount of memory and be incredibly fast, since the numbers are precomputed and access to an array is O(1).  You could also use a hash table instead of an array, but since it would be so dense and the increments would be linear, an array makes more sense to me.  If we knew we'd only have to calculate certain values, then a hash would be better.  Note that I'm going to assume that all values that are passed in are non-negative integers;  in production code, obviously, I'd add some checks for negative values, nil, or other bad values.

Johnny leapt up to the whiteboard and quickly sketched out his solution.

```
LOOKUP_TABLE = [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610]

def lookup_table_fib(x)
 if (x < LOOKUP_TABLE.length) then
   LOOKUP_TABLE[x]
 else
   nil
 end
end
```

INTERVIEWER: Umm,  very good, but I forgot to mention a constraint – it needs to be able to calculate Fibonacci numbers up to an arbitrary x.  Lookup tables wouldn't work in that case.

JOHNNY: Very true.  Luckily, there's a closed-form solution!  All you have to do is calculate the golden ratio to the power of x, then divide by the square root of five.  Since there is always some imprecision with floating point arithmetic, and the difference between successive Fibonacci numbers only approaches the golden ratio, you need to round it to the nearest integer at the end, though.

Once again, Johnny quickly wrote down the solution on the board.

```
def closed_form_fib(x)
  ((PHI ** x) / (Math.sqrt(5))).round
end
```

INTERVIEWER: Umm, yes, very nice, but we were looking for a solution without using rounding.  Could you do that without using the .round function?

JOHNNY: Sure.  You could get the same answer by adding 1/2 to the result, and then just getting the integer part of the result by chopping off any extraneous decimal places.  This should give you the same answer without using the .round function.

Johnny modified the code already up on the board to display:

```
def closed_form_no_rounding_fib(x)
  (((PHI ** x) / (Math.sqrt(5))) + 0.5).floor
end
```

Johnny beamed with pride.  He thought he was doing pretty well.  However, a quick glance over at the interviewer, and he could see some displeasure.  His interviewer had a very particular solution in mind, and it hadn't been presented yet.

INTERVIEWER: What I'm looking for is really an entirely different approach than what you're doing.  Can you think of another way to approach the problem?

JOHNNY: Umm… I suppose you could think of the closed-form solution in a backwards sort of way, by figuring out the logarithm base phi (where phi is the Golden Ratio), and keep checking numbers.  Each time the answer increments, you'd have a new Fibonacci number.  It's more of an inelegant, brute force solution, though.

```
def brute_force_fib(x)
 if (x == 0) then
   0
 elsif (x == 1 or x == 2) then
   1
 else
   cur = 1
   while (true)
     cur += 1
     if (Math.log(((cur * Math.sqrt(5)) + 0.5), PHI).floor == x) then
       return cur
      end
    end
  end
end
```

INTERVIEWER: Yes, I suppose that is also correct.  However, it's still not what I'm looking for.  Could you do this without any rounding, without any floating point arithmetic whatsoever, and without referencing the golden ratio?

JOHNNY: Of course!  It will be a little bit more inefficient, though.  You could just loop around and keep adding the last two elements according to the Fibonacci generation sequence.  I'm not sure why you'd want to go through the extra work, but I suppose it would make sense if, say, floating-point arithmetic was very time-consuming, or this was a very simple machine without the ability to do floating-point arithmetic.

```
def loop_fib(x)
  if (x == 0) then
    0
  elsif (x == 1 or x == 2) then
    1
  else
    n1, n2, cur = 1, 1, 0
    (x - 2).times do
      cur = n1 + n2
      n2, n1 = n1, cur
    end
    cur
  end
end
```

JOHNNY: Of course, with such a computationally intensive function, I'd probably want to memoize it.  Do you want me to add that in?

INTERVIEWER: No, that's not what I'm looking for.  We're running out of time, so let me just write down what we're looking for.

```
def recursive_fib(x)
  if (x == 0) then
    0
  elsif (x == 1 or x == 2) then
    1
  else
    recursive_fib(x - 1) + recursive_fib(x - 2)
  end
end
```
JOHNNY: Well, that's a pretty poor solution, if you don't mind me saying.  Even if the language has tail-call optimization, you're not going to be able to take advantage of it.  You wanted values of arbitrary n to be returned, but you're going to have stack overflows left and right once you have non-trivial n.  It's also going to be really slow compared to a closed-form solution, as well as resource-intensive.  While it's computationally intensive, it doesn't even store previously computed values, so you're going to have to run the same computations over and over again.  That just seems like recursion for recursion's sake, even though it's not really a good fit for the problem.

INTERVIEWER:  Umm.. thank you for your time.  Do you have any questions for me?

Note: I thought about doing the solutions in Haskell instead of Ruby, but if your interviewer lets you answer your questions in Haskell, you probably don't have a bad interviewer.

Note 2: Code is available on my Github at [https://github.com/laboon/FibFun](https://github.com/laboon/FibFun "FibFun")

Note 3: This is not meant to reflect poorly on anyone out there that has interviewed me in the past.  I just needed a foil for the story. =)
