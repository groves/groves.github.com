---
layout: post
title: ibug for the Mobile Safari of Today
---

Joe Hewitt's bridge to get Firebug like features on the iPhone,
[ibug](http://www.joehewitt.com/blog/firebug_for_iph.php), is delightful. Unfortunately, recent additions to Mobile Safari block its use of iframes to transmit data from the phone to the server and vice-versa. I've gotten it working again using XMLHttpRequest from the desktop browser and jsonp-like script tags on the phone.

Using the updated version differs from Joe's original description only in download location:

1. [Download ibug](http://github.com/groves/ibug/tarball/master)
1. Untar it
1. cd into its directory
1. Run `python ibug.py launch`
1. Add the printed snippet of script to the pages you want to log from

Enjoy classic logging based debugging on your phone-of-the-future today!

