---
title: 'Being naïve about Python object identities, references and sizes.'
date: 2008-12-29T10:55:00.007-08:00
draft: false
url: /2008/12/being-nave-about-python-object.html
tags: 
- common subexpression elimination
- python
---

I'm ashamed of how naïve and ignorant I stand regarding Python stuff. At least this time, I've learned a great lesson about object identities, references and sizes. Hopefully you won't make the same mistake as I did.  
  
Here's what I wanted to do: write an in-memory simplistic log keeper for my application, that would be used as a poor man's brute force protection. The details don't matter much. All this meant is I'd have to keep, say, all the last 10 authentication attempts for all users, for as long as needed, in memory.  
  
I was then a bit worried about memory usage, so I got creative. I'm probably the [electrician sysadmin that Tom referred](http://everythingsysadmin.com/2008/10/system-administration-needs-mo.html). Sometimes the wires burst in flames when I get creative.  
  
A normal (and sane) person would write something like this:  
  
```
  
UserLog(object):  
  def \_\_init\_\_(self, auth\_result, auth\_timestamp, client\_ip):  
    self.auth\_result = auth\_result  
    self.auth\_timestamp = auth\_timestamp  
    self.client\_ip = client\_ip  

```  
It's a simple class type object. It would be used this way:  
  
```
  
lastlog = UserLog(True, 1231700497, '127.0.0.1')  
print lastlog.client\_ip     

```  
  
But then I looked at the object names, "client\_ip", "auth\_result", "auth\_timestamp". And I thought: OMG, I'd have one UserLog object instance for each auth event, for each user. I don't want to waste so many precious bytes by keeping these object names in my structure!  
  
So this stupid person had the following reasoning to try and save memory.  
  
Instead of keeping a proper structured and clean "log" object with each log component with their names, I thought I should throw everything inside a list, and use the index numbers to reference to each member. For example:  
  
```
  
user\_logs\['yves'\] = \[False, 1231700497, '10.0.0.33'\]  

```  
  
In order to add mnmemonics for this stupid structure, I'd use some constants that I could use to refer to the indexes. Eg:  
  
```
  
AUTH\_TIMESTAMP = 1  
  
for log in user\_logs\['yves'\]:  
    print log\[AUTH\_TIMESTAMP\]  

```  
  
Although this hideous code is common for C programmers, it's a deadly sin for Python programmers. What kind of creature would NOT use a dictionary to store this data? That creature is me, Yves, and I ask for your forgiveness.  
  
Of course I should have used a dictionary. A class type object would be even cleaner. So I rewrote that portion of the code.  
  
The truth is the overhead for keeping 1 million instances of the string constant 'client\_ip' is not as big as I thought, even when compared to keeping 1 million instances of the integer 2. Why is that?  
  
It's simple. As we know, Python uses references for keeping its objects. We'd have 1 million references to "client\_ip", yes, but they all point to exactly the same object: the string constant "client\_id" is kept just once in memory, for all our instances.  
  
It's easy to see this happening:  
  

>   
> \>>> a = 'gggggggggggggg'  
> \>>> b = 'gggggggggggggg'  
> \>>> c = 'xxxxxxxxxxxxxx'  
> \>>> id(a)  
> 154424848  
> \>>> id(b)  
> 154424848  
> \>>> id(c)  
> 154424888  

  
  
Note how a and b refer to the same object id. One can even compare them by using 'is'.  
  

>   
> \>>> a is b  
> True  

above  
  
So although a and b are independent containers, they point to the same string constant, which is only saved once in memory. Interesting, hun? This is called [common subexpression elimination](http://en.wikipedia.org/wiki/Common_subexpression_elimination) and it's a well-known optimization technique for compilers.  
  
Obviously, although we don't have to worry about repeated string constants for the keys in our multiple dictionaries, there's still a storage overhead of using a dictionary compared to using a list to keep objects, but this is also minimal.  
  
  
Bottom-line: don't bother too much about creating your own "memory efficient" data structure instead of using a good and old dictionary (or similar, such as a class type). It's usually not worth trouble.  
  
Curiously, I noticed the common subexpression elimination does not always happen, and sometimes identical string constants are stored in different places in memory:  
  
```
  
\>>> a  
'my string'  
\>>> b  
'my string'  
\>>> a is b  
False  
\>>> id(a)  
154424808  
\>>> id(b)  
154424928  

```  
  
  
Something to discuss in a later post, I guess.
