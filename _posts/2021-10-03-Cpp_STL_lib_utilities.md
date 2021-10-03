---
layout: post
title: Basic C++ STL Library Utilities
categories: [Tech]
---

## Vectors

Include:

```c++
#include<vector>
```

Initialize/Declare:

```c++
vector<int> A={1, 2, 3, 4};
vector<int> B(82, 0);	// Vector of size 80, initialized with 0
```

Pushing a value at the end:

```c++
A.push_back(100);	// A = {1, 2, 3, 4, 100}
```

SORT:

```c++
#include<algorithm>
sort(A.begin(), A.end());  // O(Nlog(N))
```

SEARCH:

```c++
#include<algorithm>
binary_search(A.begin(), A.end(), 3);  // O(log(N)); RETRUNS: BOOLEAN
```

Now lets push in some more values:

```c++
A.push_back(100);
A.push_back(100);
A.push_back(100);
A.push_back(120)
```

Now `A={1, 2, 3, 4, 100, 100, 100, 100, 120}`



Finding 1st element **>=** 100:

```c++
vector<int>::iterator it = lower_bound(A.begin(), A.end(), 100);	// O(N) ; RETURNS mem value of 1st 100
```

Finding 1st element **>** 100:

```c++
vector<int>::iterator it = upper_bound(A.begin(), A.end(), 100);	// O(N) ; RETURNS mem value of 120
```



Therefore to find the number of occurrences of a value, we can first sort a vector, then find the difference between the values returned by lower_bound and upper_bound function with the value as input.



## Set

- ALWAYS Sorted

- NO Duplicates

- O(N)

Include:

```c++
#include<set>
```

Initialize/Declare:

```c++
set<int> S {1, 2, 3, 4, 100, 500, 1000};
```

Pushing a value at the end:

```c++
S.insert(100);	// S {1, 2, 3, 4, 100}
```

SEARCH:

```c++
auto it = S.find(2);  // O(log(N)); RETRUNS: Iterator

if (it == S.end()){ cout << "This happens when Not Found in Set!" }
```

Finding 1st element **>=** 100:

```c++
set<int>::iterator it = S.lower_bound(100);	// O(N) ; RETURNS mem value of 100
```

Finding 1st element **>** 100:

```c++
set<int>::iterator it = S.upper_bound(1000);	// O(N) ; RETURNS mem value of 1000
```



## Pair

Include:

```c++
include<pair>
```

Initialize/Declare:

```c++
pair<int, int> P1;
P1.insert({1, 5});

pair<int, char> P2;
P1.insert({1, 'a'});
```

Accessing pair elements:

```c++
P1.first = 1;
P1.second = 5;
```



Mixing Sets and Pairs:

```c++
set<pair<int, int>> S; 		// Set of pairs
```

Comparison:

*Pair {a, b} is smaller than Pair {c, d} iff (a<b) OR (a==b && c<d)*



## Map

- Like python dictionary
- Stores (key, value) pairs
- No duplicate keys allowed
- Implemented using Binary Search Tree (BST)
- O(log N)

Include:

```c++
#include<map>
```

Initialize/Declare:

```c++
map<int, int> A;

A[1]   = -1;
A[40]  = 2;
A[-20] = 200;

map<char, int> A;

A['a']   = -1;
A['b']  = 2;
A['c'] = 200;
```



Using Map, finding the number of instances of each character in a string:

```c++
map<char, int> M;
string x="jeet majumdar";

for (char c: x){
    M[c]++;
}
```



## Unordered Map:

- Implemented using Hashing functions
- Essentially an array is used at the end for bookeeping
- Fast than Maps
- O(1) 

Include:

```c++
include<unordered_map>
```

Usage is similar to Map

Read: https://www.modernescpp.com/index.php/more-special-friends-with-std-map-and-std-unordered-map

*Ref: https://www.modernescpp.com/index.php/more-special-friends-with-std-map-and-std-unordered-map*

![Summary](https://miro.medium.com/max/700/1*2vTAptn2X3HFr7iV_rkwPA.png)

*Ref: Table from: “ [STL Container Performance](https://john-ahlgren.blogspot.com/2013/10/stl-container-performance.html) ”*