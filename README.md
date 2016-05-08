# operator-overloading-demo

Demonstrates how to use define-operator and babel-plugin-operator-overloading.

## Setup

__.babelrc__
```json
{
  "presets": ["es2015", "stage-1"],
  "plugins": ["transform-decorators-legacy", "babel-plugin-operator-overloading"]
}
```

## `@operator` decorator
This high level API can be used to overload operators for new style ES2015 classes.

__point.js__
```javascript
'use overloading';

// contains necessary polyfills
require('define-operator');

class Point {
    constructor(x, y) {
        Object.assign(this, { x, y });
    }
    
    @operator('+')
    add(other) {
        return new Point(this.x + other.x, this.y + other.y);
    }
    
    @operator('-')
    sub(other) {
        return new Point(this.x - other.x, this.y - other.y);
    }
    
    @operator('-')
    neg() {
        return new Point(-this.x, -this.y);
    }
    
    @operator('*', Number)  // specify that `a` is a Number
    scale(a) {
        return new Point(a * this.x, a * this.y);
    }

    toString() {
        return `(${this.x}, ${this.y})`;
    }
}

const a = new Point(5, 10);
const b = new Point(2, 3);

const c = a + b;

console.log(`c = ${c}`);    // (7, 13);
```

## `Function.defineOperator`
This low level syntax can be used to define operators prototype style classes or built-ins.

```javascript
Function.defineOperator = function(operator, constructors, implementation) { };
```

__set-additions.js__
```javascript
Function.defineOperator('|', [Set, Set], (a, b) => new Set([...a, ...b]));
Function.defineOperator('&', [Set, Set], (a, b) => new Set([...a].filter(item => b.has(item))));
Function.defineOperator('-', [Set, Set], (a, b) => new Set([...a].filter(item => !b.has(item))));
Function.defineOperator('^', [Set, Set], (a, b) => new SuperSet([
    ...[...a].filter(item => !b.has(item)),
    ...[...b].filter(item => !a.has(item))
]));

const setToString = (set) =>  `{${[...set].join(', ')}}`;

const U = new Set([1, 1, 2, 3]);
const V = new Set([3, 5, 8]);

console.log(`U | V = ${setToString(U | V)}`); // { 1, 2, 3, 5, 8 }
console.log(`U & V = ${setToString(U & V)}`); // { 3 }
console.log(`U - V = ${setToString(U - V)}`); // { 1, 2 }
console.log(`U ^ V = ${setToString(U ^ V)}`); // { 1, 2, 5, 8 }
```
