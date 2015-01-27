---
layout: post
title:  "Speleobox"
date:   2015-01-26 10:00:45
categories:
  - art
  - apogaea
---

I don't remember where I first heard of the concept of the Speleobox.  Thus far
I've described it to 10+ people and I've yet to communicate the vision clearly.
I'll try again here today.

A speleobox could be thought of as a cave simulator.  It is a box, often
constructed from wood, that has an entrance hole and an exit hole.  Between
these, is a passageway.  This passageway will typically be small enough that
you have to crawl through it.

Consider an eight foot cube.  Subdivide it into 2 foot cubes, giving you a
total of 64 cubes.  If each cube represents two feet of passageway, then what
you end up with is 128 linear feet of cave.

There has been a small part of my brain devoted to this idea for a few years.
*(similarly, part of my brain is always designing a
trebuchet/treehouse/apocalypse-shelter)*.  This is the year all that planning
finally pays off.  I put in for an art grant from [Apogaea][apogaea] in the
[Big Money Round][big-money-round] so fingers crossed.

The part that I'm currently proudest of is the design.  The entire thing is
comprised of two foot cubes.  There are two types of cubes (strait and turn).
The cubes themselves are made up of 3 different types of panels.  And the
panels themselves are made of of a total of 7 different sizes of 1/2 inch
plywood and 2 x 2.

![Speleobox Parts]({{ site.url }}/assets/speleobox-parts.jpg)

When I was starting to come up with designs, one of the first things I did was
figure out a path through the cube.  It took me the better part of an hour with
a pad of paper.  Figuring out notation for how to represent a 3D shape was the
first problem.  The second was figuring out a path that filled the space
without leaving any cells unvisited.  I also wanted to ensure that the path
contained a few key elements.

- A strait horizontal section that spanned the whole cube.
- A drop from the top level to the bottom level of the cube.
- A climb from the bottom level to the top level of the cube.

Having built the prototype sections, and realizing how modular the components
were going to be, I wanted to know what other configurations were possible.
Manually figuring out another path seemed like solving the same puzzle twice
(as well as just being tedious).  Good thing computers like tedious tasks.

I wrote small program to find paths through speleoboxes for me.  You can find
it [here on Github][speleobox-github].  The path finding is by no means
elegant.  It merely traverses the cube randomly, until it either finds a path
that fills the space or finds itself in a situation where it has become
impossible to visit all cells.  In that case, it backtracks to the last known
good position and tries a different path.

I started it searching for paths through a 4x4x4 cube at the beginning of this
blog post and it has found 14 so far.  **MUCH** faster than doing it by hand.
I was worried that the paths it found would be boring, but so far, it's found
some great ones.

- [path-1.txt]({{ site.url }}/assets/speleobox-paths/path-1.txt)
- [path-2.txt]({{ site.url }}/assets/speleobox-paths/path-2.txt)
- [path-3.txt]({{ site.url }}/assets/speleobox-paths/path-3.txt)
- [path-4.txt]({{ site.url }}/assets/speleobox-paths/path-4.txt)
- [path-5.txt]({{ site.url }}/assets/speleobox-paths/path-5.txt)
- [path-6.txt]({{ site.url }}/assets/speleobox-paths/path-6.txt)
- [path-7.txt]({{ site.url }}/assets/speleobox-paths/path-7.txt)
- [path-8.txt]({{ site.url }}/assets/speleobox-paths/path-8.txt)
- [path-9.txt]({{ site.url }}/assets/speleobox-paths/path-9.txt)
- [path-10.txt]({{ site.url }}/assets/speleobox-paths/path-10.txt)
- [path-11.txt]({{ site.url }}/assets/speleobox-paths/path-11.txt)
- [path-12.txt]({{ site.url }}/assets/speleobox-paths/path-12.txt)
- [path-13.txt]({{ site.url }}/assets/speleobox-paths/path-13.txt)
- [path-14.txt]({{ site.url }}/assets/speleobox-paths/path-14.txt)

For the innogural setup, I'm going to plan on using the path I designed by
hand, but I'm exited about alternate configurations, or even non-standard
configurations that don't necessarily form a cube, or even a normal geometric
shape.

If you want to come give it a try, you'll have to come to Apo.  Hope to see you
there.


[apogaea]: http://apogaea.com/
[big-money-round]: http://apogaea.com/art-installations/creativegrants/#big
[speleobox-github]: https://github.com/pipermerriam/speliobox
