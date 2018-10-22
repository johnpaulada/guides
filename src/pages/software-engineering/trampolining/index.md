---
title: Trampolining
---

## Trampolining

### Problem
Recursion, which is the act of a function calling itself, is an integral part of programming and a lot of algorithms rely on recursion for its simplest implementation. One problem with recursion is not all languages optimize for it. This results in the call stack overflowing, resulting in a break in the code execution.

#### Example
If you run this in ES5 and below code, you will experience a call stack overflow:

```javascript
function fact(x) {
    if (x <= 1) {
        return 1;
    } else {
        return x * fact(x-1);
    }
}

console.log(fact(10)); // 3628800
console.log(fact(10000)); // RangeError: Maximum call stack size exceeded
```

### Solution
To get around this problem, we can use a technique called **trampolining**.
We convert a function into a trampoline by using a `trampoline()` function.

Here is an example of that function in ESNext:

```javascript
const trampoline = fn => (...args) => {
  let result = fn(...args)

  while (typeof result === 'function') {
    result = result()
  }

  return result
}
```

To create a trampolined factorial function, we have to modify our `fact()` function first, changing the recursive case to return a function which returns a value, instead of the value itself. It would look like this:

```javascript
function fact(x, n) {
    if (x < 2) {
        return n;
    } else {
        return function() { return fact(x-1, x * n); };
    }
}
```

Then you use the `trampoline()` function like this:

```javascript
const trampolinedFactorial = trampoline(fact)
```

Then just call `trampolinedFactorial()` with the values you'd use for the new `fact()` function. Example:

```javascript
trampolinedFactorial(10000, 1) /// Infinity
```

### Why this works
Returning a function allows instead of the value, allows us to keep calling the function repeatedly until it doesn't return a function, which means that the base case has been reached and a definite answer was discovered. We can do this by using an iterative looping method, checking whether the type of the value returned is still a function. It just keeps bouncing on and on until you finish, hence the name *trampolining*.

#### More Information:
- <a href='https://blog.logrocket.com/using-trampolines-to-manage-large-recursive-loops-in-javascript-d8c9db095ae3' target='_blank' rel='nofollow'>Using trampolines to manage large recursive loops in JavaScript</a>
