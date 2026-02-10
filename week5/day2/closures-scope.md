# Closures & Scope Chains

## Objectives
- Explain **lexical scope** and how JavaScript builds scope chains.
- Define a **closure** and show how inner functions “remember” variables from their outer scopes.
- Leverage closures for **data privacy**, **partial application**, and **factory functions**.
- Be aware of potential **memory implications** of lingering closures.

---

## 1. Lexical Scope & Scope Chains

Lexical scope means a function’s accessible variables are determined by its physical position in the source code. When you reference a variable inside a function, JavaScript looks through the scope chain:

1. **Local scope**: Does the function itself declare it?
2. **Outer scopes**: Walk up each enclosing function’s scope.
3. **Global scope**: Finally, the global object (`window` in browsers).

### The Scope Chain in Action
```javascript
const globalVar = '🌎';

function outer() {
  const outerVar = '🌟';

  function inner() {
    const innerVar = '🚀';
    console.log(innerVar);   // "🚀" — own scope
    console.log(outerVar);   // "🌟" — outer’s scope
    console.log(globalVar);  // "🌎" — global scope
  }

  inner();
}

outer();
```

> [!IMPORTANT]
> Even if you call `inner()` somewhere else, its scope chain remains pointing back to `outer()` and the global scope, not where it’s invoked.

---

## 2. What Is a Closure?

A closure is created when an inner function retains access to variables from its outer function after the outer has returned.

```javascript
function makeGreeter(name) {
  const greeting = 'Hello';

  return function() {
    console.log(`${greeting}, ${name}!`);
  };
}

const greetAlice = makeGreeter('Alice');
greetAlice(); // "Hello, Alice!"
```

**How it works:** Here, `makeGreeter` runs and returns the inner function—but the `greeting` and `name` variables persist in memory, accessible to that returned function.

---

## 3. Common Closure Patterns

### 3.1 Data Privacy (Module Pattern)
You can hide internal state from the global scope:

```javascript
function createCounter() {
  let count = 0; // private variable

  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    getValue() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getValue());  // 2
// counter.count is undefined — truly private!
```

### 3.2 Function Factories & Partial Application
Pre-fill some arguments and return a new function:

```javascript
function multiply(a) {
  return function(b) {
    return a * b;
  };
}

const double = multiply(2);
console.log(double(5)); // 10

// With ES6 Arrow Syntax
const multiplyArrow = a => b => a * b;
const triple = multiplyArrow(3);
console.log(triple(4)); // 12
```

---

## 4. Memory Considerations

Closures keep referenced variables alive even after the outer function ends. While powerful, overusing large closures or creating many unused ones can increase memory usage.

> [!TIP]
> Null out references if you no longer need them to allow Garbage Collection (GC) to reclaim memory:
> ```javascript
> let hugeDataHandler = createHandler(largeData);
> // … later …
> hugeDataHandler = null; 
> ```

---

## 5. In-Class Activity: `makeCounter` & `once` Utilities

### `makeCounter()`
Returns a function that, when called, increments and returns an internal count.
```javascript
const ctr = makeCounter();
console.log(ctr()); // 1
console.log(ctr()); // 2
```

### `once(fn)`
Takes any function `fn` and returns a new function that calls `fn` **only on the first invocation** and returns the same result thereafter.
```javascript
function greet() { console.log('Hello!'); return 'done'; }
const greetOnce = once(greet);
greetOnce(); // logs "Hello!", returns 'done'
greetOnce(); // logs nothing, returns 'done'
```

---

## 6. Assignment: Bank Account Module

**Goal:** Create a `createBankAccount(initialBalance)` function that returns an object with methods:
- `deposit(amount)`
- `withdraw(amount)`
- `getBalance()`

**Requirements:**
1. Prevent withdrawing more than the balance.
2. Ensure `initialBalance` defaults to `0` if not provided.
3. Use closures to keep `balance` inaccessible from outside.

**Stretch Goal:**
- Add a `transactionHistory()` method that returns an array of past transactions (e.g., `"Withdrew 30"`). Use closures to keep the history private too.

