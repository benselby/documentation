# Python 2-3 Transition

**page is work in progress**

On January 1st 2020, Python will stop maintaining Python 2. [(If you want to feel a sense of urgency, click here.)](https://pythonclock.org/) Much of our lab's code (especially datman) is written in Python 2.7, and some is written in Python 2.6. We have decided to transition to Python 3, and intend to make datman 1.0 fully Python 3.

There are some important coding habits that you should adapt to to ease the transition to Python 3, as well as some things worth understanding about the differences between Python 2 and 3.

## Really Relevant Differences

Thankfully, most 2.7 code will run fairly smoothly in 3.whatever+ with minimal changes. However, it is important to be aware of the differences for a couple reasons:
	(a) Python 3 is the future, so it is worth shifting to Python 3 styles and conventions ASAP.
	(b) if you are still working with Python 2 legacy code, you will be able to avoid writing code 	that might create problems in the future and fix problematic code as you go.

Listed here are some of the more superficial differences between Python 2/3 – however, it is worth looking at an overview if you are interested in learning more. Here is a [quick overview](url), [an in depth one](http://sebastianraschka.com/Articles/2014_python_2_3_key_diff.html), and [another in depth](http://python3porting.com/differences.html) explanation of the differences.  Below are some worth noting.

* ```print( ) ``` requires parentheses now.
*  variables inside of list comprehensions [x for x in range(...)] no longer become global
* ```open()``` from the ```io``` module should be used for file input and output
* Dividing two integers can return a float now! Eg 1/2 >> 0.5 instead of 0. To force them to return an integer use //.
* Cannot compare unorderable types
* round uses different behaviour now!!!

# Useful Links

* Compatible style tips
	** https://wiki.python.org/moin/PortingToPy3k/BilingualQuickRef
	** http://python-future.org/compatible_idioms.html

* Converting to Python 3 Recommendations
	** https://docs.python.org/3/howto/pyporting.html