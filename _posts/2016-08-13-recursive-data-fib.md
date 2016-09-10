---
title: "Data in Recursive Algorithms"
layout: post
description: "I wanted to take a close look at how data is handled in recursive algorithms."  
redirect_from: ["/2016/08/13/recursive-data-fib.html"]

---
## Motivation
While practicing for technical interviews, one of the biggest points of confusion when approaching recursive algorithms is how to pass data around the call stack. I am comfortable with the algorithmic part of recursion thanks to my background in mathematics, where induction is indispensible. Inductive reasoning when applied to engineering problems is less intuitive. So, I have decided to sit down and take some time to look at some examples of recursive algorithms and establish patterns for handling data within them.

## Fibonacci Numbers

### Example 1 : Nth Fibonacci Number

Calculating the nth Fibonacci number is (nearly) every beginner's introduction to recursion. Consider the following  code:

~~~~
 def fib(n):
     if n==1 or n==2:
         return 1
     return fib(n-1) + fib(n-2)
~~~~

Simple enough. The data being passed around is the Fibonacci number being returned through the recursive call. The only "data structure" is the call stack itself. Now let's modify our function specification slightly and observe the change in implementation.

### Example 2: Nth Fibonacci Number, Memoization with an External Cache

At first glance, this is easy. Instead of returning the sum of the two function calls, we will store their sum in a variable, print that result, then return the value:

~~~~
 def fib_0toN(n):
     if n==1 or n==2:
         return 1
     next = fib_0toN(n-1)+fib_0toN(n-2)
     print next
     return next
~~~~
However, running this will return *every* Fibonacci number that this inefficent approach calculates. The following is (part of!) the output of `fib_0toN(10)`:

`...34
2
3
2
5
2
3
8
2
3
2
5
13
2
3
2
5
2
3
8
21
55
55`

At this point, it would be best to implement memoization to eliminate the need to recusrively calculate Fibonacci numbers for each call to the function:

~~~~
 # Fib memoized, cache empty initially
 # Code from http://ujihisa.blogspot.com/2010/11/memoized-recursive-fibonacci-in-python.html
 cache = {}
 def fib_cache(n):
     if n in cache:
         return cache[n]
     else:
         cache[n] = n if n < 2 else fib_cache(n-1) + fib_cache(n-2)
         print cache[n]
         return cache[n]   
~~~~

Returns:
`1
0
1
2
3
5
8
13
21
34
55
55`

There are some rough edges but this does what we want it to do. Note that instead of the if statement in the `else` block we could have initialized the cache before calling the function. Either way, we can see that an external data structure is needed to keep track of the Fibonacci numbers being calculated. 

The immediate function now first checks to see if it needs to actually make a recursive call by checking the cache. If the parameter `n` is not in the cache, this function makes the recursive call(s) and stores the value in the cache before returning that value. 

Here, the _external_ data structure is used to store data in a scope outside of the call stack. Let's take a closer look at how the cache is filled as the function recurses by adding some `print` statements:

~~~~
 # Fib memoized, cache empty initially
 # Code from http://ujihisa.blogspot.com/2010/11/memoized-recursive-fibonacci-in-python.html
 cache = {}
 def fib_cache(n):
     if n in cache:
         print 'We found {0} in the cache. Its fib num is {1}'.format(n, cache[n])
         return cache[n]
     else:
         print 'We could not find {0} in the cache.'.format(n)     
         cache[n] = n if n < 2 else fib_cache(n-1) + fib_cache(n-2)
         print cache[n]
         return cache[n]
~~~~

This returns:

~~~~
We could not find 10 in the cache.
We could not find 9 in the cache.
We could not find 8 in the cache.
We could not find 7 in the cache.
We could not find 6 in the cache.
We could not find 5 in the cache.
We could not find 4 in the cache.
We could not find 3 in the cache.
We could not find 2 in the cache.
We could not find 1 in the cache.
1
We could not find 0 in the cache.
0
1
We found 1 in the cache. Its fib num is 1
2
We found 2 in the cache. Its fib num is 1
3
We found 3 in the cache. Its fib num is 2
5
We found 4 in the cache. Its fib num is 3
8
We found 5 in the cache. Its fib num is 5
13
We found 6 in the cache. Its fib num is 8
21
We found 7 in the cache. Its fib num is 13
34
We found 8 in the cache. Its fib num is 21
55
55
~~~~
 
As you would expect, the cachce is empty initially, so the function must recurse down to the base case before the cache becomes useful. Once the cache starts being filled, we can use it to avoid blowing up the call stack with repeated work. 

### Example 3: Nth Fibonacci Number, Memoization with an Internal Cache

In , we can give our Fibonacci function an optional parameter that is set to `None` by default if that parameter is omitted in a function call. Thus rather than define `cache` outside of the function, we can move it inside as such:

~~~~
 # Fib memoized, internal cache 
 def fib_internal(n, cache=None):
     if not cache:
         cache = {}
         cache[1] = 1                         
         cache[2] = 1                         
     if n in cache:
         print 'We found {0} in the cache. Its fib num is {1}'.format(n, cache[n])
         return cache[n]
     else:
         print 'We could not find {0} in the cache.'.format(n) 
         cache[n] = fib_internal(n-1, cache) + fib_internal(n-2, cache)
         return cache[n]
~~~~
 
This function runs near-as-makes-no-difference identically to the previous function. Because of the optional parameter, we can call the function the same way: `fib_internal(10)`.	

## Conclusion

Calculating Fibonacci numbers is not a very difficult problem to solve. However, taking a close look at the data structures involved in the recursive implementations helps us to apply these patterns to more difficult recursive problems. 

We have seen that passing data through `return` works well if you only care about that value in the context of the immediate function. If you want to implement memoization, or keep track of values for data on each level of the call stack, use a cache. This cache can be defined outside of the function body, or within the function body using optional parameters. 

In the future, we will explore more complicated recursive algorithms as well as more complicated use-cases of caches. When possible, we will relate back to these basic patterns. 

## References

"If I have seen further it is by standing on the sholders [sic] of Giants." - Sir Issac Newtown (and many before him)

[5 Ways of Fibonacci in Python](https://technobeans.wordpress.com/2012/04/16/5-ways-of-fibonacci-in-python/)

[Fibonacci Sum with Memoization](http://codereview.stackexchange.com/questions/95554/fibonacci-sum-with-memoization)

[Memoized Recursive Fibonacci in Python](http://ujihisa.blogspot.com/2010/11/memoized-recursive-fibonacci-in-python.html)
