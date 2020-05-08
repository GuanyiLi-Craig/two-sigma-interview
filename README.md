# Two sigma questions from Head Hunter



1. Sort a list of words and integers and keep the relative order of words and integers, plus your data-center screwed up and know every data center needs to have the data from the other centers again, what is the most efficient way to copy data such that every center has it.
2. Given a set of \(start, end\) pairs of timestamps where end &gt; start, write an algorithm to return the smallest set of disjoint intervals containing every pair of \(start, end\) in the original set.
3. string compression - huffman /lempel ziv
4. Reg expression matching algorithm \(parse string, recursion, some edge cases, check jvm implementation\)
5. Write a program that takes a list of strings containing integers and words and returns a sorted version of the list. The goal is to sort this list in such a way that all words are in alphabetical order and all integers are in numerical order. Furthermore, if the nth element in the list is an integer it must remain an integer, and if it is a word it must remain a word.
6. centers using as few copies as possible. Example 1: ---------- Input: 4 1 3 4 1 2 3 1 3 1 4 2 One Possible Correct Output: 2 2 1 4 1 2 2 2 3 4 4 3 3 1 4 done
7. [https://www.glassdoor.co.uk/Interview/Bitwise-Operation-Add-one-to-a-number-without-using-QTN\_1217211.htm](https://www.glassdoor.co.uk/Interview/Bitwise-Operation-Add-one-to-a-number-without-using-QTN_1217211.htm)
8. the most unexpected one was "what is Godel's incompleteness theorem?
9. Find all the subsets of size k
10. Construct tree from given array. Write a method for removing a subtree, given the root of the subtree.
11. How would you design a class/methods for reverse Polish notation? \(They explained what reverse Polish notation was first, of course\)
12. Design question: how could you extend your implementation of reverse Polish notation to include more operators?
13. Implement reverse Polish notation using only arithmetic operations
14. Design LRU
15. Design an ATM
16. Find max benefit from 2 transactions given some stock prices
17. How would you calculate the mean on a continuous stream
18. Write code to process multiple interleaving streams of data \(from a text file\) and output statistics on each stream.
19. For the architecture question, Conway's Game of Life algorithm was considered. I was asked to write pseudo code for tackling this problem at a micro-level \(e.g. one machine\) and a large-scale level \(e.g. distributed system
20. Code a function that matches regular expressions with targets. There is a DP and a recursive solution.
21. postifix computer
22. Code a postfix notation calculator. Doable with a stack. Now, what if you wanted to support arbitrary operations on some variable number of preceding numbers? How does the code change?
23. You are given a tree in the form of a list of \(value, parentndx, intree\) tuples, where intree is a boolean denoting whether the node is in the tree, and parentndx is the location in the list of the parent node of this node. The root's parent is -1. Write a function remove\(ndx\) that removes the node at that ndx and all its children in O\(n\). It requires caching which nodes have been visited and a recursive function that checks whether a node in the list should be removed \(terminates on parentndx==-1 \|\| ndx or when it finds a cached node that was already removed and visited\)
24. You are given two infinite streams of data, where each datum has fields \(timestamp, value\). The streams have a single function take\(\) that pops the oldest thing off and returns it or "blocks" until something arrives in the queue for it to return. Data might arrive much later than its timestamp. You are given a function output\(a,b\), which takes two values, one from each stream, and computes something. You want to call output\(\) on all \(a,b\) pairs that have timestamps less than some given interval apart, and you want to do this as soon as any data that completes such a pair arrives. Construct pseudocode that will do this. The answer involves two threads that each manage a list of numbers pulled off their stream, pull a number from the other thread's list, try to match it against everything, and then carefully discard data when appropriate
25. You are managing a webservice and get a complaint about the page loading slowly. What is a possible cause of the problem? How would you check that? Okay, say that's not the problem. What else could it be?
26. add 2 numbers without using +

