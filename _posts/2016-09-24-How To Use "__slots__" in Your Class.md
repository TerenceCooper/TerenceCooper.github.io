---
layout: post
title: "How to use __slots__ in your own class"
comments: true
tag: [programming, technique, python]
---

By default, Python stores instance attributes in a per-instance dictionay named __dict__. Dictionaries, nonetheless, have a significant memory overhead because of the underlying hash table used to provide fast access. If you are dealing with millions of instances with few attributes, the __slots__ class attribute can save a lot of memory, by letting the interpreter store the instance in a tuple instead of a dict. Let's check out how to achieve this.
<!--more-->
To define __slots__ you create a class attribute with that name and assign it an iterable of `str` with identifiers for the instance attributes. I like to use a tuple for that, because it conveys the message that the __slots__ definition cannot change.  

EXAMPLE:  
    ```class MyClass:  
        __slots__ == ('__attrName1', '__attrName2')  
	...  
    ```  
By defining __slots__ in the class, you are telling the interpreter: "These are all the instance attributes in this class." Python then stores them in a tuple-like structure in each instance, avoinding the memory overhead of the per-instance __dict__. This can make a huge difference in memory usage if you have millions of instances active at the same time.

There is another special per-instance attribute that you may want to keep: the __weakref__ attribute is necessary for an object to support weak references. That attribute is present by default in instances of user-defined classes. However, if the class defines __slots__, and you need the instances to be targets of weak references, then you need to include '__weakref__' among the attributes named in __slots__.

To summarize, if well used __slots__ may provide significant memory savings, but there are a few caveats:
   ** You must remember to redeclare __slots__ in each subclass, since the inherited attribute is ignored by the interpreter.
   ** Instances will only be able to have the attributes listed in __slots__, unless you inclued '__dict__' in __slots__ -- but doing so may negate the memory savings.  
   ** Instances cannot be target of weak references unless you remember to inclued '__weakref__' in __slots__.
If your program is not handling millions of instances, it's probably not worth the trouble of creating a somewhat unusual and tricky class whose instances may not accept dynamic attributes or may not support weak references. Like any optimization, __slots__ should be used only if justified by a present need and when its benifit is proven by careful profiling.

