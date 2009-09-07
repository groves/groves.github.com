---
layout: post
title: Wesabe Export Downloader
---

[Wesabe](http://wesabe.com) has an excellent interface to sanitize and categorize your financial information, so I've been using it for the past couple months to keep track of the dollars around here. They also allow you to download all of your data in a tidy XML format that contains all of the tagging and naming information you've added through said excellent interface. Since the reports they offer are still pretty basic, I've taken to chopping up the XML a little in Python to produce more complicated views of where the money has gone month by month, category by category. 

The tedium of logging into the site, clicking through to the export page and actually downloading
the XML every time I wanted to produce an updated report started to wear on me a bit though.
[grabber.py](http://sevorg.org/wesabe/grabber.py) is a dumb little script that takes your Wesabe username and password as arguments, logs into wesabe, and downloads the XML as accounts.xml. They also have a csv format which grabber.py will grab if you pass it 'csv' as the final argument.

I've got another script that actually does more interesting things like parsing the XML with elementtree and making reports. I'll probably polish it up in the next few days and post it here as well.

