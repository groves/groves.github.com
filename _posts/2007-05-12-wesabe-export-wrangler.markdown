---
layout: post
title: Wesabe Export Wrangler
---

I just uploaded [gleaner.py](http://sevorg.org/wesabe/gleaner.py), a Python module to parse [Wesabe's](http://wesabe.com/) XML export and make little text reports based on their content. It really only consists of two things at this point: read_txactions(), a function that reads "accounts.xml" in the current directory and returns a list of Transaction objects, and a set of "filter functions" that operate on the Transaction objects. 

The filter functions are the ones that are in all caps. So far I have TAG, MERCHANT, ACCOUNT, AFTER, BEFORE, MONTH, AND, OR and NOT. To use a filter function, you call it with what you want to match and it returns a new function that returns true when passed Transactions that meet that criteria and false for those that don't make the grade. In the example below, TAG('charlie') creates a function that returns true for any transaction that has the 'charlie' tag. MERCHANT and ACCOUNT are fairly similar to TAG. They take a piece of text and return true if a transaction's merchant is the same as that text or its account is the same respectively. AFTER and BEFORE take a day formatted like '2007-05-01' and make a filter that matches transactions after or before that date. MONTH matches any transaction in a given month. It either takes a full month like '2007-05' or just a month number like '5'. If given the second form, it assumes the month is in the current year.

An example report is probably better than actually talking through this:

{% highlight python %}
from gleaner import *

txs = read_txactions()

total = 0
for month in [MONTH(m) for m in '3', '4', '5']:

    spending = sum(t.amount for t in filter(AND(TAG('charlie'), month), txs))

    print '%s: %s' % (month.name, spending)
    total += spending

print 'Total: ', total
{% endhighlight %}

I tag everything I spend on myself with 'charlie'. So
`AND(TAG('charlie'), month)`
just matches what I spent on myself in a given month. When I run it(with fictional data), the output is:

$ python charlie_report.py
during Mar 2007: -457.23
during Apr 2007: -318.45
during May 2007: -412.58
Total:  -1188.26

Of course, that's pretty easily figured out from the things built-in to Wesabe's site. Here's a more complicated report:

{% highlight python %}
from gleaner import *

txs = read_txactions()

def sum_txactions(*filters):
    return sum(t.amount for t in filter(AND(*filters), txs))

def month_summary(name, income, spendable, spent, diff):

    sep = '=' * (len(name) + 2)

    print '''%(name)s
%(sep)s
Income: %(income)s
Spendable: %(spendable)s
Spent: %(spent)s
Diff: %(diff)s''' % locals()
{% endhighlight %}
{% highlight python %}
net = 0

computer_spending = sum_txactions(TAG('computer'), TAG('chelsea'))

for month in [MONTH(m) for m in '3', '4', '5']:

    income = sum_txactions(TAG('birdnerd'), month)
    computer_deduct = 200

    #Don't deduct for March since computer was purchased after that
    if 'Mar' in month.name:
        computer_deduct = 0

    spendable = income/2 - computer_deduct
    spent = sum_txactions(TAG('chelsea'), month, NOT(TAG('computer')))

    diff = spendable + spent
    month_summary(month.name, income, spendable, spent, diff)

    net += diff
    computer_spending += computer_deduct

print 'Remaining Laptop: %s' % computer_spending

print 'Net: %s' % net
{% endhighlight %}

My lovely wife sells her art through the net under the moniker birdnerd. She wants to put half of her birdnerd profits back into the family's coffers and then spend the other half on things that strike her fancy. To throw a wrench into the works, she bought a most excellent MacBook a month ago and would like to pay for that $200 a month out of the "strike her fancy" half of the birdnerd profits. This report prints out the birdnerd income month by month, figures out how much of that she can spend on herself minus the monthly laptop payment, and keeps track of how much of the laptop remains to be paid off to print at the end.

Right now this doesn't handle using tag splits to determine amounts since the tag splits aren't actually in the XML export. The Wesabeans saw the use of that when I mentioned it to them, so they're going to add it at some point in the future. Next up for me is to add some chartsengrafs to the output mix.

