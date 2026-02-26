# Promises - A container for future value

A promise is an object that represents eventual completion or failure of an asynchronous operation.

## Why promises introduced?
- Callback hell (Pyramid of Doom)
- Inversion of Control
- Chaining was difficult

## States of Promise:
- Pending
- Fulfilled
- Rejected

<span style="color: #ba66ff">*Note: Once promise is settled either Resolved or Rejected it cannot change again. (Immutable)*</span>

## Creating a Promise:
```js
    const promise = new Promise((resolve, reject) => {
        /**
         *  some instructions...
         *  ...
         *  ...
         *  Let's assume, it's success.
         */
        const success = true;
        if (success) resolve("Success");
        else reject("Failure");
    });
```

- The function passed in <span style="color: #00d592">**Promise( ```//executor function as callback``` )**</span> constructor is known as - **Executor Function**

- **Executor Function** executes immediately as soon as the **Promise()** constructor invokes

- It gets 2 parameters by **Promise** class, <code style="color: #0f0;">**resolve**</code> & <code style="color: #f00;">**reject**</code>
    - <code style="color: #0f0;">**resolve**</code>: to execute with data when async operation succeed
    - <code style="color: #f00;">**reject**</code>: to execute with error when async operation failed

    <span style="color: #ba66ff">*Note: These both are <strong>Static methods</strong>*</span>

## Consuming a Promise: