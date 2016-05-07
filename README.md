# operator-overloading-demo

Demonstrates how to use define-operator and babel-plugin-operator-overloading.

## Example

__.babelrc__
```json
{
  "presets": ["es2015", "stage-1"],
  "plugins": ["transform-decorators-legacy", "babel-plugin-operator-overloading"]
}
```

__example.js__
```javascript
'use overloading';

// contains necessary polyfills
require('define-operator');

class Point {
    constructor(x, y) {
        Object.assign(this, { x, y });
    }

    toString() {
        return `(${this.x}, ${this.y})`;
    }
}

Function.defineOperator('+', [Point, Point], (a, b) => new Point(a.x + b.x, a.y + b.y));

const a = new Point(5, 10);
const b = new Point(2, 3);

const c = a + b;

console.log(`c = ${c}`);    // (7, 13);
```
