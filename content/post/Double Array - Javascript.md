---
title: "Double Array - Javascript"
date: 2018-07-22
tags: ["double", "array", "javascript", "es6", "js"]
draft: false
---

Double Array In Javascript
------

Here's a fun question to test your JS skills:

Given an array of numbers, you'll have to `double the array.`
Example: given [5,6,7,8,9], your function should return [5,6,7,8,9,5,6,7,8,9].

Approach 1:
-----

```

var  array  = [5,6,7,8,9]
for(let elements =  0; elements < array.length; elements++)
{
	array.push(array[elements]);
}
console.log(array);

```

Do you see a pitfall here?

This ends up running in an `infinite loop`. You're using the same array length after pushing the elements into the array, resulting in an infinite loop.

Approach 2:
-----

```

var array = [1,2,3,4,5]
let length = array.length;
for(let elements = 0; elements < length; elements++)
{
    array.push(array[elements]);
}
console.log(array);

```

This ends up running fine as you're taking the `array length` before iterating the array and value of the length variable stays the same.

Approach 3:
-----

```

Array.prototype.double = function() {
    let length = this.length;

    for (let elements = 0; elements < length; elements++) {
        this.push(this[elements]);
    }
    return this;
}

var array = [5,6,7,8,9]
console.log(array.double())

```

You can make use of `Javascript prototype chain` and use it essentially more on the lines of an extension method without having to pass anything to the function. We have access to the array that the method is being called on with this.

Approach 4:
-----

```

var doubleArray = function(array){
  let doubledArray = array.concat(array);
  return doubledArray;
}
var array = [5,6,7,8,9]
console.log(doubleArray(array))

```

In case, you wish to do this without `for loop`, we can make use of the concat function to achieve the same.

All the ways shown above have the same runtime complexity of:

`O(n) = linear`

End of the day, it's about knowing how to solve problems in `more than one way`.