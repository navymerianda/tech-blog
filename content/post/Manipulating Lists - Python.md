---
title: "Manipulating Lists - Python"
date: 2018-07-28
tags: ["copy", "python", "deep copy", "shallow copy"]
draft: false
---

Manipulating lists in python is a tricky affair if enough attention isn't paid at the time of copying and changing lists.

Today, we'll look at two possible real world scenarios and what's the best way to go about manipulating lists in python.

Scenario 1:
-----

Let's consider the below snippet

```
independentList = ["a", "c", "z"]

dependentList = independentList

dependentList[1] = "p"

print(independentList)

O/P : ['a', 'p', 'z']
```

As you can see in this case, though we didn't intend to change the independent variable, we ended up altering both dependent and independent variable, because in both independent and dependent lists we are only `storing the references to the values stored in the memory`, we're not literally storing the values in the lists.

Scenario 2:
----
Here's a valid approach to the above problem

```
independentList = ["a", "c", "z"]

#dependentList = list(independentList)
#dependentList = independentList[:]

dependentList[1] = "p"

print(independentList)
O/P: ['a', 'c', 'z']

print(dependentList)
O/P: ['a', 'p', 'z']
```

As you can see here, we are using l`ist and another notation[ : ]` to keep away from `referencing issues.` In this case, we'll not face any issue with respect to any manipulation on the `lists as both would operate independently.` It's okay to use the list keyword or [ : ] to create a `deep copy of the independent list.` 