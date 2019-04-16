# Data Structures and Algorithms

Using this [free course](https://classroom.udacity.com/courses/ud513) from Microfost and this [textbook](https://opendatastructures.org/) by Pat Morin to review algorithms before diving too deep into my machine learning curriculum. 

## Introduction

From the textbook:

*Number of operations: Imagine an application with a moderately-sized data set, say of one million (106), items. It is reasonable, in most appli- cations, to assume that the application will want to look up each item at least once. This means we can expect to do at least one million (106) searches in this data. If each of these 106 searches inspects each of the 106 items, this gives a total of 106 × 106 = 1012 (one thousand billion) inspections.*

*Processor speeds: At the time of writing, even a very fast desktop computer can not do more than one billion (109) operations per second. This means that this application will take at least 1012/109 = 1000 seconds, or roughly 16 minutes and 40 seconds. Sixteen minutes is an eon in computer time, but a person might be willing to put up with it (if he or she were headed out for a coffee break).*

When studying the performance of a data structure, there are three things that matter most:
1. Correctness: The data structure should correctly implement its interface.
2. Time complexity: The running times of operations on the data structure should be as small as possible.
3. Space complexity: The data structure should use as little memory as possible.

### Efficiency (complexity)

BigO notation is the way programmers and computer scientists discuss efficiency. It's written with an `O` followed by an algebraic expression that explains how the algorithm scales with increased data. Essentially, for a complex equation for runtime, the element in the equation which domainates as x goes to infinity is the result when talking about BigO.

Ex: `5nlog(n)+8n−200` runs in `nlog(n)` time 


### Common Data Interfaces

The **Interface** describes the set of interactions you can have with the data structure (add, remove, etc.). The **Implementation** is how the data structure is actually constructed, and the same interface can be implemented in many different ways.

The queue, stack and deck are different examples of data structure arrays with different rules for adding and removing values from the array. In this book, he focuses on the **list** to represent linear sequences, since it's a more general form of all of these. Operations allowed on the list:
1. Size: return length of list
2. Get(i): return the ith vaule
3. Set(i, x): set item i in the list to the value x
4. Add/Remove(i, x): add/remove value x at position i

![list](./img/list_imp.png)

The **Unordered Set** contains n distinct elements; no element appears more than once; the elements are in no specific order. The **Ordered Set** is similar, but keeps the elements in the set sorted for easy searching.

*The distinction between the USet and SSet find(x) operations is very important and often missed. The extra functionality provided by an SSet usually comes with a price that includes both a larger running time and a higher implementation complexity. For example, most of the SSet implementations discussed in this book all have find(x) operations with run- ning times that are logarithmic in the size of the set. On the other hand, the implementation of a USet as a ChainedHashTable in Chapter 5 has a find(x) operation that runs in constant expected time. When choosing which of these structures to use, one should always use a USet unless the extra functionality offered by an SSet is truly needed.*

![uset](./img/uset_imp.png)

![sset](./img/sset_imp.png)


