## Fun with JavaScript Type System

## Intro to the JS type system
Compared to the other articles in this series, this article includes a set of short notes (recipes) that are useful to understand the Type system in JavaScript.

It is a common misconception that JS does not have *types*. In contrast to typed languages, js follows a slightly different approach in handling types. As a ground rule, I usually adhere to the following statement when thinking about types in JS. 

> JavaScript variables do not have types, but the values those variables hold do!.

*I found the above statement in the book [You Don't Know JS (book series) - 1st Edition](https://github.com/getify/You-Dont-Know-JS/tree/1st-ed) written by  [Kyle Simpson](https://me.getify.com/). As a matter of fact, I was motivated to write this article after reading that book, and most of the examples are taken from it. I recommend reading that amazing book if you intend to be a professional JS developer.*

If you have the basic knowledge of JS, you might already have understood what the above statement means. Also, you must be aware that the values of JS variables can be in one of the below data types.

1. null
1. undefined
1. boolean
1. number
1. string
1. object
1. symbol

Let's consider the following.

```js
let myVar = 42;
```

The above line of code assigns the value 42 (`number` value) to the variable called `myVar`. The implicit knowledge that says *myVar* at this moment is a `numeric` type, is emerged from the *value* of that variable. When declaring `myVar` we did not specify any *type* information into it. The language also allows us to assign a value that has a completely different type later in the execution too. So doing the following is acceptable.

```js
let myVar = 42; // At this point, myVar is a number
myVar = {key: 42, name: 'John'}; // Now myVar is an object
```

What this basically tells us is that JS variables do not belong to any *type*, but their values do! This is how the JS type system is basically different from statically-typed languages. 

Compared to other languages, some design decisions are taken to make the language behave in unconventional ways when it comes to the type system. Javascript frequently gets humiliated/harassed on the internet by non-experienced developers due to the lack of understanding about uncommon parts of the type system.

If you properly learn these edge-cases/caveats in the system, you will understand the underlying reasoning behind most of them. Without putting the blame on the language, we can learn its beauty and make our coding life fun! ;-)

## Fun with type checking

Let's quickly see some interesting and fun characteristics of the JS type system now.

### `typeof` - inspect the type of a value

The construct called `typeof` is used to inspect the type of a given value.

```js
console.log(typeof undefined); //undefined
console.log(typeof true); //boolean
console.log(typeof 42); //number
console.log(typeof '42'); //string
console.log(typeof { life: 42 }); //object
console.log(typeof Symbol()); //symbol
```

In addition to inspecting the data types, it can detect whether the value is a `function` too.

```js
console.log(typeof function() {}); //function
```

But, there is a caveat when it comes to handling `null` since `typeof` sees `null` as an `object` type.

```js
console.log(typeof null); //object
```

*This behavior has been identified as a historical bug. But if the language tries to fix it now, it will break a considerable amount of existing apps that use this buggy behavior.*

### `undefined` vs. undeclared

JavaScript already has a data type called `undefined`. It is the default value that is assigned to any variable that has not been initialized yet. 

In contrast, undeclared means that the variable is not *declared* in the code (the variable does not exist or is not defined). It is safe to access undefined variables, but if you try to access an undeclared variable, the JS interpreter will throw an error. See below.

```js
let myVar;

console.log(myVar); // undefined
console.log(someOtherVar); // ReferenceError: someOtherVar is not defined
```

P.S.: If you try to inspect an undeclared variable, it will be detected as `undefined` and no error will be thrown. This is used as a safe-guard (precaution) so that the developer can check the existence of an unknown variable without causing an error in the program.

## Fun with arrays

Arrays are a special variant of the `object` data type in JS. Let's look into some interesting facts about arrays now.

## `delete` array elements 

Deleting array elements using the `delete` construct will not *remove* the element from the array. Instead, it will assign `undefined` to the particular element, which in return will not affect the array length too.

```js
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

delete arr[0];
delete arr[3];
delete arr[9];

console.log(arr); // [, 2, 3, , 5, 6, 8, 9, ]
console.log(arr.length); // 10
console.log(typeof arr[3]); // undefined
```
## `string` indexes are not counted towards the length of an array

```js
const arr = [1, 2, 3];

arr['val1'] = 'val1';
arr['val2'] = 'val2';

console.log(arr.length); // 3
```

But, if the index can be *interpreted* as a `numeric` value, that element is accounted for the length.

```js
const arr = [1, 2, 3];

arr['3'] = 'val1'; // '3' can be intepreted as a numeric value.
arr['val2'] = 'val2';

console.log(arr.length); // 4
```

## Fun with Strings

Strings and arrays behave in a similar way in some cases.

```js
var str = "foo";
var arr = ["f", "o", "o"];

str.length; // 3
arr.length; // 3

str.indexOf("o"); // 1
arr.indexOf("o"); // 1

var c = str.concat("bar"); // "foobar"
var d = arr.concat(["b", "a", "r"]); // ["f","o","o","b","a","r"]

str === c; // false
arr === d; // false

str; // "foo"
arr; // ["f","o","o"]
```

Quickly reverse a string using array methods. (This will not work for strings having non Unicode characters though)

```js
var c = 'foo'
	// split `a` into an array of characters
	.split( "" )
	// reverse the array of characters
	.reverse()
	// join the array of characters back to a string
	.join( "" );

c; // "oof"
```

## Fun with Numbers

Numbers can be specified in exponent form,

```js
var onethousand = 1E3;						// means 1 * 10^3
var onemilliononehundredthousand = 1.1E6;	// means 1.1 * 10^6
```

Most of the languages that follow the IEEE 754 standard have the floating-point precision issue.

```js
0.1 + 0.2 === 0.3; // false
```

### Number ranges

Numbers have ranges (min/max values).  

Max and min floating-point values that can be represented in JS are,

```js
console.log(Number.MAX_VALUE); // 1.7976931348623157e+308
console.log(Number.MIN_VALUE); // 5e-324
```

Max and min integer values that can be safely used in calculations in JS are,

```js
console.log(Number.MAX_SAFE_INTEGER); // 1.7976931348623157e+308
console.log(Number.MIN_SAFE_INTEGER); // 5e-324
```

## Fun with special values

### Non-value values

`null` and `undefined` are considered the non-value values in JS. 

In non-strict mode, it is possible to assign a value to the globally defined `undefined` value! Oops!

```js
function foo() {
	undefined = 2; // really bad idea!
}

foo();
```

Even in strict mode, it is possible to re-declare the `undefined` variable and assign values to it. Even worse!

```js
function foo() {
	"use strict";
	var undefined = 2;
	console.log( undefined ); // 2
}

foo();
```

### The real `undefined`

The `void` operator when used against a variable will return `undefined`.

```js
const a = 42;

console.log(a); // 42
console.log(void a); // undefined
```

### Special numbers

`NaN` is used to specify a non-numeric result from a mathematical expression in JS.


```js
const a = 42;

console.log(a * 'four'); // NaN
```

But JS identifies `NaN` as a number. Ouch!

```js
const a = 42;
const b = a * 'four';
console.log(b); // NaN
console.log(typeof b); // number
```

More surprisingly, `NaN` is not equal to itself. It is the only value in JS that has a non-reflexive characteristic.

```js
console.log(NaN === NaN); // false
console.log(NaN == NaN); // false
```

Therefore, in order to check whether a value is `NaN` we have to use the `isNaN` function.

### To the `Infinity` and beyond

JavaScript does not complain you about dividing by zero. It returns you a special value called `Infinity` if you do so. To be precise, if you divide a negative value by zero, JS will return `-Infinity` too!

```js
const a = 42;

console.log(a / 0); // Infinity
console.log(-a / 0); // -Infinity
```

JavaScript also has the concept of negative zero.


```js
const a = 42;

console.log(-a * 0); // -0
```

But `-0` is equal to `0` when checked for equality!

```js
console.log(-0 === 0); // true
```

Since `0` and `NaN` behaves in weird ways when checked for equality, we have the `Object.is()` function to check the absolute equality of values.

```js
console.log(Object.is(NaN, NaN)); // true
console.log(Object.is(-0, 0)); // false
console.log(Object.is(42, 42)); // true
console.log(Object.is(42, '42')); // false
console.log(Object.is('foo', 'foo')); // true
```

## Value vs. Reference

Unlike in other languages, JS treats value and reference depending on the type of value that is passed on. Simply put, 

* If we pass an object type (including arrays, functions, etc.) to a function, it is treated as a reference (updates to the value will affect the outer scope value).
* If we pass a primitive type (number, string, etc.), to a function, it is treated as a value.

```js
function foo (obj) {
  obj.val = 42;
}

function bar (num) {
    num = 42;
}

const o = {val: 1};
const n = 1;

foo(o);
bar(n);

console.log(o.val); // 42
console.log(n); // 1
```

## Summary

As you have seen above, JS type system behaves in odd ways when dealing with certain values. We cannot put the blame on the designers of the language for most of the above cases. Instead, we should more focus on being aware of such behaviors and write our programs to avoid bugs due to them. 

If you are in doubt or have confusion, you can always refer to the  [language specification](https://262.ecma-international.org/12.0/) (latest 2021 ECMASCRIPT Spec) and try to understand the logical reasoning behind such behaviors. If the spec defines a behavior, we cannot call it a surprise. It should be the standard. If we carefully understand and be aware of the pitfalls, we can become professional JS developers with very little to complain about.

Thanks for reading :)


































