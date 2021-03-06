---
title: Those Who Don't Know Their UNIX History Are Doomed to Re-type It
layout: default
---

This is a bit of a diversion from my series on probabilistic data structures (I promise, I'm working on putting an article on skip lists together), but I wanted to write about something which I think could benefit your UNIX (or UNIX-esque, e.g., GNU/Linux) development environment immensely.

It turns out that not everybody who uses the UNIX command line knows about the wealth of history commands available.  Used properly, you should rarely ever have to re-type a full command, and if you make a quick typo, you will be able to correct it almost on the fly. Note that these commands are not universal (especially if you're using one of the odder shells, with which I am not very familiar – I've really only ever used sh, bash, and tcsh), but should work on bash.  I tried these out on both Ubuntu 12.04 and Mac OS X 10.6 without difficulty, but your mileage may vary.  If you don't know which shell you're using, you can try either of the following commands:

```
echo $SHELL
```

or

```
ps -p $$
```

The first tip, and probably the most common, is the use of the !-operators.  These refer to previous commands in a variety of ways.  In case you didn't know, there is a list of commands that you recently typed.  This can easily be accessed by the command history.  You can also add an integer argument to limit it to the last n commands. For example, here is the output of running history 5 on my machine.

```
$ history 5
 496 clear
 497 ruby -w BayesianClassifier.rb
 498 vi foo
 499 wc foo
 500 ping google.com
```

Here you can see that for some reason, I cleared the screen, ran a Ruby program, edited a file, ran a word count on it, and pinged google.com.  All quotidian stuff.  If I wanted to re-edit foo, I could type:

```
!498
```

and command number 498 would run again, opening up the foo file for me to edit.

You can also edit by referring to commands relatively.  For example, let's say that instead of editing foo, I just wanted to run a word count on it again.  Instead of typing history and getting the exact number, you can type:

```
!-2
```
and run the command “two commands ago,” wc foo.  If you want to run the command immediately before, a shortcut for “run the previous command again” is !!.  For example,

```
$ touch foo
$ !!
touch foo
```
Note that whenever you use the !-operators, bash will print out the command and then execute it.  If you ONLY want to print out the command, say to double-check that it's the right one, add the :p operator to the end.

```
$ touch foo
$ !!:p
touch foo
```
This will print the command out but importantly, it will not execute it.  However, it “counts” as a command; you could print something out using :p, and then run !! to actually execute it.  From the point of view of the shell, it was the last command “executed.”
You are not limited to the strict text of the command entered.  You can specify which arguments you want to use by using the :n operator after the !-operator.  For example, let's say you want to list some files and see how big they are, then you will determine which one to edit.

```
$ ls -l billtest.rb helpers_tests.rb test_recommend.rb
-rw-r--r-- 1 wlaboon staff 320 Jan 13 18:38 billtest.rb
-rw-r--r-- 1 wlaboon staff 1732 Jan 13 09:39 helpers_tests.rb
-rw-r--r-- 1 wlaboon staff 624 Jan 13 09:39 test_recommend.rb
$ vi !:3
vi helpers_tests.rb
```

Note that the numbering starts at 0, so !:0 would be ls, !:1 would be -l, and so on.  !$ is a special command meaning the last argument – in this case, test_recommend.rb.  You can also combine operators, like so:

```
$ !763:p
ls -l billtest.rb helpers_tests.rb test_recommend.rb
$ !!:$:p
test_recommend.rb
```

The last example translates to “print out, but do not execute, the last argument to the most recently typed command.”

The ^ operator is also a very useful command.  It allows you to easily correct typos, or slightly modify the previous command.  It works by typing ^, what you wish to change, ^ again, and what you wish to change the previous string to.  This is one of those things which makes more sense when you see it in action:

```
$ la -l
-bash: la: command not found
$ ^la^ls
ls -l
total 24
-rw-r--r-- 1 wlaboon staff 320 Jan 13 18:38 billtest.rb
-rw-r--r-- 1 wlaboon staff 0 Jan 20 14:32 foo
-rw-r--r-- 1 wlaboon staff 1732 Jan 13 09:39 helpers_tests.rb
-rw-r--r-- 1 wlaboon staff 624 Jan 13 09:39 test_recommend.rb
```

Note that this only replaces the FIRST instance of the string with the second.

One of the cooler things available in modern shells is the reverse-i-search.  Type control-r at the prompt and you can interactively search for previous commands.

```
(reverse-i-search)`cblas': locate cblas.h
```

It's kind of difficult to tell, but I typed in cblas and it found the last command I used that text string in.  It also interactively shows you on the right the closest match it's found as you type.

Finally, you can modify some environment variables to make your history completion even better.  Set HISTSIZE to the number of commands you want to store in your history.  You can set HISTCONTROL to ignorespace, ignoredupes, or ignoreboth.  ignorespace will not store any commands that start with a space.  Since the shell ignores initial whitespace, this won't matter to the execution of the commands, but it won't store that command in the history.  Honestly, I've never felt much need for that, but I guess if you're embarassed about the number you times you have to run “man *command*”, then perhaps you will find it more useful than I do.  ignoredupes will not put duplicate entries into your history.  ignoreboth will (intuitively) set both the ignoredupes and ignorespace flags.  Since you don't want to keep re-typing these every time you log in, of course, you should probably add them to your .bashrc or equivalent.