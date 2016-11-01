---
layout: post
title: Nosetests and how to pass object instances between tests
subtitle: How to take your tests not only to the next level, but out of this world
tags:
- nose
- nosetests
- advanced nose
- pass object
- python
- programming
---
### What is nose?
It is an unittest framework. It makes testing Python code super easy and efficient so you can focus on the tests' quality instead of doing all of the grunt work.  
If you write python code then you should be using nose to verify not only your overall program, but parts of it as well.  

### Why do I prefer nose?
Its syntax is not only clear, but also easy to remember. It has built in support for saving the results in a nicely formatted xml file with details like how long each test took.  
Instead of writing every single error a different printing/logging message I can just throw a descriptive Exception when encountering an error and nose will fail that test, print the description in a noticeable way and move onto the next test. This way I don't have to scan through the super descriptive logs to know if the tests even passed, or failed.  

### The problem I faced while using it
Soon enough my team and I were using nose for all of our tests and when a test ran a loop of smaller tests. We used subprocess calls to initialize another one.  
Nose held up well, had no problem handling the child nose processes, but there was one problem.  
Passing variables in between noses was easy enough as long as you only want to transfer strings, just declare an environmental variable. However this just didn't cut it for us, as we use huge classes to control our equipment, initializing an instance can take anywhere in between 5 and 10 seconds. Imagine running 150 tests, those 5-10 seconds add up quickly.  
I looked everywhere for a way to pass class instances in between nosetests, but I couldn't find anything useful. Until now...  

### The solution
So I read everything about nose I could find online. The only lead I found was that nose could be ran from inside a python script, at least cutting out the middleman, subprocess.  
Then I changed options around until I found what I was looking for. Here's **how you pass class instances in between nosetests, or to a single nosetest**.  

```Python
import nose
from time import sleep

class example:
	def __init__(self):
		print "Setting up"
		# Simulating long setup script
		sleep(5)
		self.name = 'This class is just an example of what would be passed to a nosetest'
		self.title = 'This is the title'
		self.description = 'This is the description'

class test_example:
	def test_this(self):
		assert 'is' not in global_example.title
		global global_example
		global_example.description = global_example.description.replace('description', 'desc.')
	def test_that(self):
		assert 'description' not in global_example.description

if __name__ == '__main__':
	global global_example
	global_example = example()
	global_example.title = global_example.title.replace('is', 'was')
	nose.run(defaultTest = __name__ + ':test_example.test_this')
	nose.run(defaultTest = __name__ + ':test_example.test_that')
```

I hope this tutorial will help others from going through the same trouble, or from re initializing those classes many times.  
**tl;dr**
1. Make class instance *global*  
2. ```import nose```  
3. Use ```nose.run()```, or equivalent to run nose  
4. Make sure you pass ```__main__ + ":<tester class name>.<test function name>"``` to nose as *defaultTest*  
