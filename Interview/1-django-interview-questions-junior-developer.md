# Interview

## Difference between list and tuple

list is mutable but tuple is immutable

## What is zip?

The zip() function returns a zip object, which is an iterator of tuples where the first item in each passed iterator is paired together, and then the second item in each passed iterator are paired together etc.

```python
a = ("John", "Charles", "Mike")
b = ("Jenny", "Christy", "Monica", "Vicky")
x = zip(a, b)

# use the tuple() function to display a readable version of the result:
print(tuple(x))
# (('John', 'Jenny'), ('Charles', 'Christy'), ('Mike', 'Monica'))

print(x)
# <zip object at 0x2b0fadfff3c0>
```

## What is context manager?

Context managers allow you to allocate and release resources precisely when you want to. The most widely used example of context managers is the with statement. Suppose you have two related operations which you’d like to execute as a pair, with a block of code in between. Context managers allow you to do specifically that. For example:

```python
with open('some_file_path', 'w') as opened_file:
    opened_file.write('Hola!')
```

The above code opens the file, writes some data to it and then closes it. If an error occurs while writing the data to the file, it tries to close it. The above code is equivalent to:

```python
file = open('some_file', 'w')
try:
    file.write('Hola!')
finally:
    file.close()
```


## Have you ever implemented a context manager?

At the very least a context manager has an __enter__ and __exit__ method defined. Let’s make our own file-opening Context Manager and learn the basics.

```python
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        self.file_obj.close()
```

Just by defining __enter__ and __exit__ methods we can use our new class in a with statement.

```python
with File('demo.txt', 'w') as opened_file:
    opened_file.write('Hola!')
```

Our __exit__ method accepts three arguments. They are required by every __exit__ method which is a part of a Context Manager class. Let’s talk about what happens under-the-hood.

1. The with statement stores the __exit__ method of the File class.
2. It calls the __enter__ method of the File class.
3. The __enter__ method opens the file and returns it.
4. The opened file handle is passed to opened_file.
5. We write to the file using .write().
6. The with statement calls the stored __exit__ method.
7. The __exit__ method closes the file.

Between the 4th and 6th step, if an exception occurs, Python passes the type, value and traceback of the exception to the __exit__ method. It allows the __exit__ method to decide how to close the file and if any further steps are required. In our case we are not paying any attention to them.

What if our file object raises an exception? We might be trying to access a method on the file object which it does not supports. For instance:

```python
with File('demo.txt', 'w') as opened_file:
    opened_file.undefined_function('Hola!')
```

Let’s list the steps which are taken by the with statement when an error is encountered:

1. It passes the type, value and traceback of the error to the __exit__ method.

2. It allows the __exit__ method to handle the exception.
3. If __exit__ returns True then the exception was gracefully handled.
4. If anything other than True is returned by the __exit__ method then the exception is raised by the with statement.

In our case the __exit__ method returns None (when no return statement is encountered then the method returns None). Therefore, the with statement raises the exception:

```python
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
AttributeError: 'file' object has no attribute 'undefined_function'
```

Let’s try handling the exception in the __exit__ method:

```python
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        print("Exception has been handled")
        self.file_obj.close()
        return True

with File('demo.txt', 'w') as opened_file:
    opened_file.undefined_function()

# Output: Exception has been handled
```

Our __exit__ method returned True, therefore no exception was raised by the with statement.

This is not the only way to implement Context Managers. There is another way and we will be looking at it in the next section.

#### Implementing a Context Manager as a Generator

We can also implement Context Managers using decorators and generators. Python has a contextlib module for this very purpose. Instead of a class, we can implement a Context Manager using a generator function. Let’s see a basic, useless example:

```python
from contextlib import contextmanager

@contextmanager
def open_file(name):
    f = open(name, 'w')
    try:
        yield f
    finally:
        f.close()
```

Okay! This way of implementing Context Managers appear to be more intuitive and easy. However, this method requires some knowledge about generators, yield and decorators. In this example we have not caught any exceptions which might occur. It works in mostly the same way as the previous method.

Let’s dissect this method a little.

Python encounters the yield keyword. Due to this it creates a generator instead of a normal function.
Due to the decoration, contextmanager is called with the function name (open_file) as its argument.
The contextmanager decorator returns the generator wrapped by the GeneratorContextManager object.
The GeneratorContextManager is assigned to the open_file function. Therefore, when we later call the open_file function, we are actually calling the GeneratorContextManager object.
So now that we know all this, we can use the newly generated Context Manager like this:

```python
with open_file('some_file') as f:
    f.write('hola!')
```

## decorator

Decorators are a significant part of Python. In simple words: they are functions which modify the functionality of other functions. They help to make our code shorter and more Pythonic. Most beginners do not know where to use them so I am going to share some areas where decorators can make your code more concise.

#### Everything in Python is an object

```python
def hi(name="yasoob"):
    return "hi " + name

print(hi())
# output: 'hi yasoob'

# We can even assign a function to a variable like
greet = hi
# We are not using parentheses here because we are not calling the function hi
# instead we are just putting it into the greet variable. Let's try to run this

print(greet())
# output: 'hi yasoob'

# Let's see what happens if we delete the old hi function!
del hi
print(hi())
#outputs: NameError

print(greet())
#outputs: 'hi yasoob'
```

#### Defining functions within functions

```python
def hi(name="yasoob"):
    print("now you are inside the hi() function")

    def greet():
        return "now you are in the greet() function"

    def welcome():
        return "now you are in the welcome() function"

    print(greet())
    print(welcome())
    print("now you are back in the hi() function")

hi()
#output:now you are inside the hi() function
#       now you are in the greet() function
#       now you are in the welcome() function
#       now you are back in the hi() function

# This shows that whenever you call hi(), greet() and welcome()
# are also called. However the greet() and welcome() functions
# are not available outside the hi() function e.g:

greet()
#outputs: NameError: name 'greet' is not defined
```

#### Returning functions from within functions

It is not necessary to execute a function within another function, we can return it as an output as well:

```python
def hi(name="yasoob"):
    def greet():
        return "now you are in the greet() function"

    def welcome():
        return "now you are in the welcome() function"

    if name == "yasoob":
        return greet
    else:
        return welcome

a = hi()
print(a)
#outputs: <function greet at 0x7f2143c01500>

#This clearly shows that `a` now points to the greet() function in hi()
#Now try this

print(a())
#outputs: now you are in the greet() function
```

Just take a look at the code again. In the if/else clause we are returning greet and welcome, not greet() and welcome(). Why is that? It’s because when you put a pair of parentheses after it, the function gets executed; whereas if you don’t put parenthesis after it, then it can be passed around and can be assigned to other variables without executing it. Did you get it? Let me explain it in a little bit more detail. When we write a = hi(), hi() gets executed and because the name is yasoob by default, the function greet is returned. If we change the statement to a = hi(name = "ali") then the welcome function will be returned. We can also do print hi()() which outputs now you are in the greet() function.

#### Giving a function as an argument to another function

```python
def hi():
    return "hi yasoob!"

def doSomethingBeforeHi(func):
    print("I am doing some boring work before executing hi()")
    print(func())

doSomethingBeforeHi(hi)
#outputs:I am doing some boring work before executing hi()
#        hi yasoob!
```

#### Writing your first decorator

In the last example we actually made a decorator! Let’s modify the previous decorator and make a little bit more usable program:

```python
def a_new_decorator(a_func):

    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")

        a_func()

        print("I am doing some boring work after executing a_func()")

    return wrapTheFunction

def a_function_requiring_decoration():
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requiring_decoration()
#outputs: "I am the function which needs some decoration to remove my foul smell"

a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
#now a_function_requiring_decoration is wrapped by wrapTheFunction()

a_function_requiring_decoration()
#outputs:I am doing some boring work before executing a_func()
#        I am the function which needs some decoration to remove my foul smell
#        I am doing some boring work after executing a_func()
```

They wrap a function and modify its behaviour in one way or another. Now you might be wondering why we did not use the @ anywhere in our code? That is just a short way of making up a decorated function. Here is how we could have run the previous code sample using @.

```python
@a_new_decorator
def a_function_requiring_decoration():
    """Hey you! Decorate me!"""
    print("I am the function which needs some decoration to "
          "remove my foul smell")

a_function_requiring_decoration()
#outputs: I am doing some boring work before executing a_func()
#         I am the function which needs some decoration to remove my foul smell
#         I am doing some boring work after executing a_func()

#the @a_new_decorator is just a short way of saying:
a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
```

If we run:

```python
print(a_function_requiring_decoration.__name__)
# Output: wrapTheFunction
```

That’s not what we expected! Its name is “a_function_requiring_decoration”. Well, our function was replaced by wrapTheFunction. It overrode the name and docstring of our function. Luckily, Python provides us a simple function to solve this problem and that is functools.wraps. Let’s modify our previous example to use functools.wraps:

```python
from functools import wraps

def a_new_decorator(a_func):
    @wraps(a_func)
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction

@a_new_decorator
def a_function_requiring_decoration():
    """Hey yo! Decorate me!"""
    print("I am the function which needs some decoration to "
          "remove my foul smell")

print(a_function_requiring_decoration.__name__)
# Output: a_function_requiring_decoration
```

**Blueprint:**

```python
from functools import wraps
def decorator_name(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        if not can_run:
            return "Function will not run"
        return f(*args, **kwargs)
    return decorated

@decorator_name
def func():
    return("Function is running")

can_run = True
print(func())
# Output: Function is running

can_run = False
print(func())
# Output: Function will not run
```

Note: @wraps takes a function to be decorated and adds the functionality of copying over the function name, docstring, arguments list, etc. This allows us to access the pre-decorated function’s properties in the decorator.

#### Use-cases

Now let’s take a look at the areas where decorators really shine and their usage makes something really easy to manage.

#### Authorization

Decorators can help to check whether someone is authorized to use an endpoint in a web application. They are extensively used in Flask web framework and Django. Here is an example to employ decorator based authentication:

**Example:**

```python
from functools import wraps

def requires_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        auth = request.authorization
        if not auth or not check_auth(auth.username, auth.password):
            authenticate()
        return f(*args, **kwargs)
    return decorated
```

#### Logging

Logging is another area where the decorators shine. Here is an example:

```python
from functools import wraps

def logit(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print(func.__name__ + " was called")
        return func(*args, **kwargs)
    return with_logging

@logit
def addition_func(x):
   """Do some math."""
   return x + x


result = addition_func(4)
# Output: addition_func was called
```

#### Decorators with Arguments

Come to think of it, isn’t @wraps also a decorator? But, it takes an argument like any normal function can do. So, why can’t we do that too?

This is because when you use the @my_decorator syntax, you are applying a wrapper function with a single function as a parameter. Remember, everything in Python is an object, and this includes functions! With that in mind, we can write a function that returns a wrapper function.

#### Nesting a Decorator Within a Function

Let’s go back to our logging example, and create a wrapper which lets us specify a logfile to output to.

```Python
from functools import wraps

def logit(logfile='out.log'):
    def logging_decorator(func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            # Open the logfile and append
            with open(logfile, 'a') as opened_file:
                # Now we log to the specified logfile
                opened_file.write(log_string + '\n')
            return func(*args, **kwargs)
        return wrapped_function
    return logging_decorator

@logit()
def myfunc1():
    pass

myfunc1()
# Output: myfunc1 was called
# A file called out.log now exists, with the above string

@logit(logfile='func2.log')
def myfunc2():
    pass

myfunc2()
# Output: myfunc2 was called
# A file called func2.log now exists, with the above string
```

#### Decorator Classes

Now we have our logit decorator in production, but when some parts of our application are considered critical, failure might be something that needs more immediate attention. Let’s say sometimes you want to just log to a file. Other times you want an email sent, so the problem is brought to your attention, and still keep a log for your own records. This is a case for using inheritence, but so far we’ve only seen functions being used to build decorators.

Luckily, classes can also be used to build decorators. So, let’s rebuild logit as a class instead of a function.

```python
class logit(object):

    _logfile = 'out.log'

    def __init__(self, func):
        self.func = func

    def __call__(self, *args):
        log_string = self.func.__name__ + " was called"
        print(log_string)
        # Open the logfile and append
        with open(self._logfile, 'a') as opened_file:
            # Now we log to the specified logfile
            opened_file.write(log_string + '\n')
        # Now, send a notification
        self.notify()

        # return base func
        return self.func(*args)



    def notify(self):
        # logit only logs, no more
        pass
```

This implementation has an additional advantage of being much cleaner than the nested function approach, and wrapping a function still will use the same syntax as before:

```python
logit._logfile = 'out2.log' # if change log file
@logit
def myfunc1():
    pass

myfunc1()
# Output: myfunc1 was called

```

Now, let’s subclass logit to add email functionality (though this topic will not be covered here).

```python
class email_logit(logit):
    '''
    A logit implementation for sending emails to admins
    when the function is called.
    '''
    def __init__(self, email='admin@myproject.com', *args, **kwargs):
        self.email = email
        super(email_logit, self).__init__(*args, **kwargs)

    def notify(self):
        # Send an email to self.email
        # Will not be implemented here
        pass
```

From here, @email_logit works just like @logit but sends an email to the admin in addition to logging.

## What is singleton pattern?
A Singleton pattern in python is a design pattern that allows you to create just one instance of a class, throughout the lifetime of a program. Using a singleton pattern has many benefits. A few of them are:

- To limit concurrent access to a shared resource.
- To create a global point of access for a resource.
- To create just one instance of a class, throughout the lifetime of a program.  

Different ways to implement a Singleton:  
A singleton pattern can be implemented in three different ways. They are as follows:

- Module-level Singleton
- Classic Singleton
- Borg Singleton

#### Module-level Singleton:

All modules are singleton, by definition.  Let’s create a simple module-level singleton where the data is shared among other modules. Here we will create three python files – singleton.py, sample_module1.py, and sample_module2.py – in which the other sample modules share a variable from singleton.py.

```python
## singleton.py
shared_variable = "Shared Variable"
 singleton.py

## samplemodule1.py
import singleton
print(singleton.shared_variable)
singleton.shared_variable += "(modified by samplemodule1)"
samplemodule1.py

## samplemodule2.py
import singleton
print(singleton.shared_variable)
```

Here, the value changed by samplemodule1 is also reflected in samplemodule2.

#### Classic Singleton:

Classic Singleton creates an instance only if there is no instance created so far; otherwise, it will return the instance that is already created. Let’s take a look at the below code.

```python
class SingletonClass(object):
def __new__(cls):
	if not hasattr(cls, 'instance'):
	cls.instance = super(SingletonClass, cls).__new__(cls)
	return cls.instance

singleton = SingletonClass()
new_singleton = SingletonClass()

print(singleton is new_singleton)

singleton.singl_variable = "Singleton Variable"
print(new_singleton.singl_variable)
```

Output

```
True
Singleton Variable
```

```python
class SingletonClass(object):
def __new__(cls):
	if not hasattr(cls, 'instance'):
	cls.instance = super(SingletonClass, cls).__new__(cls)
	return cls.instance

class SingletonChild(SingletonClass):
	pass

singleton = SingletonClass()
child = SingletonChild()
print(child is singleton)

singleton.singl_variable = "Singleton Variable"
print(child.singl_variable)


```

```
True
Singleton Variable
```

Here, you can see that SingletonChild has the same instance of SingletonClass and also shares the same state. But there are scenarios, where we need a different instance, but should share the same state. This state sharing can be achieved using Borg singleton.

#### Borg Singleton:

Borg singleton is a design pattern in Python that allows state sharing for different instances. Let’s look into the following code.

```python
class BorgSingleton(object):
_shared_borg_state = {}

def __new__(cls, *args, **kwargs):
	obj = super(BorgSingleton, cls).__new__(cls, *args, **kwargs)
	obj.__dict__ = cls._shared_borg_state
	return obj

borg = BorgSingleton()
borg.shared_variable = "Shared Variable"

class ChildBorg(BorgSingleton):
pass

childBorg = ChildBorg()
print(childBorg is borg)
print(childBorg.shared_variable)
```

Output
```
False
Shared Variable
```

Along with the new instance creation process, a shared state is also defined in the __new__ method. Here the shared state is retained using the shared_borg_state attribute and it is stored in the __dict__ dictionary of each instance.

If you want a different state, then you can reset the  shared_borg_state attribute. Let’s see how to reset a shared state.

```python
class BorgSingleton(object):
_shared_borg_state = {}

def __new__(cls, *args, **kwargs):
	obj = super(BorgSingleton, cls).__new__(cls, *args, **kwargs)
	obj.__dict__ = cls._shared_borg_state
	return obj

borg = BorgSingleton()
borg.shared_variable = "Shared Variable"

class NewChildBorg(BorgSingleton):
	_shared_borg_state = {}

newChildBorg = NewChildBorg()
print(newChildBorg.shared_variable)
```

Here, we have reset the shared state and tried to access the shared_variable. Let’s see the error.

```
Traceback (most recent call last):
  File "/home/329d68500c5916767fbaf351710ebb13.py", line 16, in <module>
    print(newChildBorg.shared_variable)
AttributeError: 'NewChildBorg' object has no attribute 'shared_variable'
```

#### Use cases of a Singleton:

Let’s list a few of the use cases of a singleton class. They are as follows:

- Managing a database connection
- Global point access to writing log messages
- File Manager
- Print spooler

#### Create a Web Crawler using Classic Singleton:

Let’s create a webcrawler that uses the benefit of a classic singleton. In this practical example, the crawler scans a webpage, fetch the links associated with the same website, and download all the images in it. Here, we have two main classes and two main functions.

- CrawlerSingleton: This class acts a classic singleton
- ParallelDownloader: This class provides thread functionality to download images
- navigate_site: This function crawls the website and fetches the links that belong to the same website. And, finally, it arranges the link to download images.
- download_images: This function crawls the page link and downloads the images.  

Apart from the above classes and functions, we use two sets of libraries to parse the web page – BeautifulSoap and HTTP Client.
Note: Execute the code in your local machine

```python
import httplib2
import os
import re
import threading
import urllib
import urllib.request
from urllib.parse import urlparse, urljoin
from bs4 import BeautifulSoup

class CrawlerSingleton(object):
	def __new__(cls):
		""" creates a singleton object, if it is not created,
		or else returns the previous singleton object"""
		if not hasattr(cls, 'instance'):
			cls.instance = super(CrawlerSingleton, cls).__new__(cls)
		return cls.instance

def navigate_site(max_links = 5):
	""" navigate the website using BFS algorithm, find links and
		arrange them for downloading images """

	# singleton instance
	parser_crawlersingleton = CrawlerSingleton()

	# During the initial stage, url_queue has the main_url.
	# Upon parsing the main_url page, new links that belong to the
	# same website is added to the url_queue until
	# it equals to max _links.
	while parser_crawlersingleton.url_queue:

		# checks whether it reached the max. link
		if len(parser_crawlersingleton.visited_url) == max_links:
			return

		# pop the url from the queue
		url = parser_crawlersingleton.url_queue.pop()

		# connect to the web page
		http = httplib2.Http()
		try:
			status, response = http.request(url)
		except Exception:
			continue

		# add the link to download the images
		parser_crawlersingleton.visited_url.add(url)
		print(url)

		# crawl the web page and fetch the links within
		# the main page
		bs = BeautifulSoup(response, "html.parser")

		for link in BeautifulSoup.findAll(bs, 'a'):
			link_url = link.get('href')
			if not link_url:
				continue

			# parse the fetched link
			parsed = urlparse(link_url)

			# skip the link, if it leads to an external page
			if parsed.netloc and parsed.netloc != parsed_url.netloc:
				continue

			scheme = parsed_url.scheme
			netloc = parsed.netloc or parsed_url.netloc
			path = parsed.path

			# construct a full url
			link_url = scheme +'://' +netloc + path


			# skip, if the link is already added
			if link_url in parser_crawlersingleton.visited_url:
				continue

			# Add the new link fetched,
			# so that the while loop continues with next iteration.
			parser_crawlersingleton.url_queue = [link_url] +\
												parser_crawlersingleton.url_queue

class ParallelDownloader(threading.Thread):
	""" Download the images parallelly """
	def __init__(self, thread_id, name, counter):
		threading.Thread.__init__(self)
		self.name = name

	def run(self):
		print('Starting thread', self.name)
		# function to download the images
		download_images(self.name)
		print('Finished thread', self.name)

def download_images(thread_name):
	# singleton instance
	singleton = CrawlerSingleton()
	# visited_url has a set of URLs.
	# Here we will fetch each URL and
	# download the images in it.
	while singleton.visited_url:
		# pop the url to download the images
		url = singleton.visited_url.pop()

		http = httplib2.Http()
		print(thread_name, 'Downloading images from', url)

		try:
			status, response = http.request(url)
		except Exception:
			continue

		# parse the web page to find all images
		bs = BeautifulSoup(response, "html.parser")

		# Find all <img> tags
		images = BeautifulSoup.findAll(bs, 'img')

		for image in images:
			src = image.get('src')
			src = urljoin(url, src)

			basename = os.path.basename(src)
			print('basename:', basename)

			if basename != '':
				if src not in singleton.image_downloaded:
					singleton.image_downloaded.add(src)
					print('Downloading', src)
					# Download the images to local system
					urllib.request.urlretrieve(src, os.path.join('images', basename))
					print(thread_name, 'finished downloading images from', url)

def main():
	# singleton instance
	crwSingltn = CrawlerSingleton()

	# adding the url to the queue for parsing
	crwSingltn.url_queue = [main_url]

	# initializing a set to store all visited URLs
	# for downloading images.
	crwSingltn.visited_url = set()

	# initializing a set to store path of the downloaded images
	crwSingltn.image_downloaded = set()

	# invoking the method to crawl the website
	navigate_site()

	## create images directory if not exists
	if not os.path.exists('images'):
		os.makedirs('images')

	thread1 = ParallelDownloader(1, "Thread-1", 1)
	thread2 = ParallelDownloader(2, "Thread-2", 2)

	# Start new threads
	thread1.start()
	thread2.start()


if __name__ == "__main__":
	main_url = ("https://www.geeksforgeeks.org/")
	parsed_url = urlparse(main_url)
	main()
```

## What is solid?

- Single-responsibility Principle (SRP) states: A class should have one and only one reason to change, meaning that a class should have only one job.
- Open-closed Principle (OCP) states: Objects or entities should be open for extension but closed for modification.
- Liskov Substitution Principle states: Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.
- Interface segregation principle states: A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.
- Dependency inversion principle states: Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

## instance vs static vs class vs abstract functions

**Instance method**
Instance methods are very basic and easy method that we use regularly when we create Class in Python. If we want to print an instance variable or instance method we must create an object of that required class. They access the unique data, i.e. Instance methods will be able to access the data and properties unique to each instance.

```python
class RECTANGLE:
    def number_of_sides(self):
        print(“I have 2 sides”)
```
1. Instance methods takes self as the first argument.
2. Decorator is not required for instance methods.  

**Static Method**
Static methods are related to a class in some way, but don’t need to access any class-specific data. i.e. self , is not neccessarily the first argument of the method and It doesn’t even need to instantiate an instance
Static methods will not be able to access anything in the claas, totally self-contained/isolated mode

```python
class RECTANGLE:
     def number_of_sides(self):
          print(“I have 2 sides”)
     @staticmethod
     def info():
         print("inside the Square class")
```

1. Use static method when there is a method inside a class that is logically related to the class, but does not necessarily interact with any specific instance.
2. Static methods are created using the @staticmethod decorator.


**Class method**
A class method is a method that is bound to the class and not the class’s object. Class methods know about their class. i.e. They can’t access specific instance dataIn class method the first argument in the function parameters is class, It’s an implicit first argument.
Class methods can access limited methods in the specified class, it can modify class specific details

```python
class RECTANGLE:
    name = "RECTANGLE"
    def number_of_sides(self):
        print(“I have 2 sides”)

    @classmethod
    def info_class_name(cls):
        print(cls.name)
```

1. Class methods are created using the @classmethod decorator.
2. The class method has access to the class’s state as it takes a class parameter that points to the class and not the object instance.

**Abstract method**
An abstract method is a method that has a declaration but does not have an implementation. While we are designing large functional units, we use an abstract class. An abstract class can be considered as a blueprint for other classes.
In Python Abstract class doesn’t have straight foreward implementation, We need to import abc(abstract base class). ABC works by decorating the base class’s methods as abstract and then registering concrete classes as implementations of the conceptual base.

```python
from abc import ABC, abstractmethod
class POLYGON(ABC):
    @abstractmethod
    def number_of_sides(self):
        pass
class RECTANGLE(POLYGON):
    def number_of_sides(self):
        print(“I have 2 sides”)
```

1. Abstract methods are created using the @abstractmethod decorator.
2. They override the properties of base class.

## lambda functions

Lambdas are one line functions. They are also known as anonymous functions in some other languages. You might want to use lambdas when you don’t want to use a function twice in a program. They are just like normal functions and even behave like them.

**Blueprint**

```python
lambda argument: manipulate(argument)
```

**Example**

```python
add = lambda x, y: x + y
print(add(3, 5))
# Output: 8
```

Here are a few useful use cases for lambdas and just a few ways in which they are used in the wild:

**List sorting**

```python
a = [(1, 2), (4, 1), (9, 10), (13, -3)]
a.sort(key=lambda x: x[1])
print(a)
# Output: [(13, -3), (4, 1), (1, 2), (9, 10)]
```

**Parallel sorting of lists**

```python
data = zip(list1, list2)
data = sorted(data)
list1, list2 = map(lambda t: list(t), zip(*data))
```

## Dictionary shallow vs deep copy

By "shallow copying" it means the content of the dictionary is not copied by value, but just creating a new reference.

```
>>> a = {1: [1,2,3]}
>>> b = a.copy()
>>> a, b
({1: [1, 2, 3]}, {1: [1, 2, 3]})
>>> a[1].append(4)
>>> a, b
({1: [1, 2, 3, 4]}, {1: [1, 2, 3, 4]})
```

In contrast, a deep copy will copy all contents by value.

```
>>> import copy
>>> c = copy.deepcopy(a)
>>> a, c
({1: [1, 2, 3, 4]}, {1: [1, 2, 3, 4]})
>>> a[1].append(5)
>>> a, c
({1: [1, 2, 3, 4, 5]}, {1: [1, 2, 3, 4]})
```

1. **b = a**: Reference assignment, Make a and b points to the same object.
2. **b = a.copy()**: Shallow copying, a and b will become two isolated objects, but their contents still share the same reference
3. **b = copy.deepcopy(a)**: Deep copying, a and b's structure and content become completely isolated.


## Django architecture

MVT (Model View Template)

## Name some Django commands

> **django -admin startproject PROJECT_NAME** Start a project
**python manage.py startapp APP_NAME** Start an app
**python manage.py runserver** Run a development server
**python manage.py makemigrations** Configures and creates the basic migrations and preps the database for changes.
**python manage.py migrate** This is what actually enforces those changes and applies changes to our database.

## Q Query

#### Complex lookups with Q objects

Keyword argument queries – in filter(), etc. – are “AND”ed together. If you need to execute more complex queries (for example, queries with OR statements), you can use Q objects.
A Q object (django.db.models.Q) is an object used to encapsulate a collection of keyword arguments. These keyword arguments are specified as in “Field lookups” above.
For example, this Q object encapsulates a single LIKE query:

```
from django.db.models import Q
Q(question__startswith='What')
```

Q objects can be combined using the &, |, and ^ operators. When an operator is used on two Q objects, it yields a new Q object.
For example, this statement yields a single Q object that represents the “OR” of two "question__startswith" queries:

```
Q(question__startswith='Who') | Q(question__startswith='What')
```

This is equivalent to the following SQL WHERE clause:

```
WHERE question LIKE 'Who%' OR question LIKE 'What%'
```

You can compose statements of arbitrary complexity by combining Q objects with the &, |, and ^ operators and use parenthetical grouping. Also, Q objects can be negated using the ~ operator, allowing for combined lookups that combine both a normal query and a negated (NOT) query:

```
Q(question__startswith='Who') | ~Q(pub_date__year=2005)
```

Each lookup function that takes keyword-arguments (e.g. filter(), exclude(), get()) can also be passed one or more Q objects as positional (not-named) arguments. If you provide multiple Q object arguments to a lookup function, the arguments will be “AND”ed together. For example:

```
Poll.objects.get(
    Q(question__startswith='Who'),
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
)
```

… roughly translates into the SQL:

```
SELECT * from polls WHERE question LIKE 'Who%'
    AND (pub_date = '2005-05-02' OR pub_date = '2005-05-06')
```

Lookup functions can mix the use of Q objects and keyword arguments. All arguments provided to a lookup function (be they keyword arguments or Q objects) are “AND”ed together. However, if a Q object is provided, it must precede the definition of any keyword arguments. For example:

```
Poll.objects.get(
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6)),
    question__startswith='Who',
)
```

… would be a valid query, equivalent to the previous example; but:

```
# INVALID QUERY
Poll.objects.get(
    question__startswith='Who',
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
)
```

… would not be valid.

**See also** [The OR lookups examples](https://github.com/django/django/blob/main/tests/or_lookups/tests.py) in Django’s unit tests show some possible uses of Q.



[reference1](https://book.pythontips.com/en/latest/index.html)
[reference2](https://medium.com/nerd-for-tech/python-instance-vs-static-vs-class-vs-abstract-methods-1952a5c77d9d)
[reference3](https://www.geeksforgeeks.org/singleton-pattern-in-python-a-complete-guide/)
[reference4](https://stackoverflow.com/questions/3975376/understanding-dict-copy-shallow-or-deep)
[reference5](https://docs.djangoproject.com/en/dev/topics/db/queries/#complex-lookups-with-q-objects)
