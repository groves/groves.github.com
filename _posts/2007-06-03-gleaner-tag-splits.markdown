---
layout: post
title: gleaner.py - tag splits and docstrings! 
---
I've just updated [gleaner.py](http://sevorg.org/wesabe/gleaner.py), my wrangler for Wesabe's XML. The gentlemen hackers at Wesabe were good enough to add tag splits to the wealth of information already contained in their XML export which necessitated an update. 

There were only a couple changes needed to take advantage of it. First, the TAG filter grew some logic to reject anything tagged with a split value of 0. Any existing code should take advantage of this automatically. Results will just be a little more accurate now if you've used a one-time tag with a value of 0 to cancel out a recurring tag for a single transaction. Second, Txaction got a "total" method that takes TAG filters. Call it with TAGs for the splits you want to include, and it'll return the sum of the values of each of those tags on the Txaction.

If that's sounding a little hazy, hopefully the second group of changes I made will help with that. gleaner actually has docstrings like a good Python module now! Fire up the Python interpreter and call help to your heart's content.

The Wesabeans also added account balances to the XML last week, so some code to pull that out will probably come next. I can't write my "real-time pile of gold doubloons purchasable by all worldly resources" dashboard widget until I have that information.


