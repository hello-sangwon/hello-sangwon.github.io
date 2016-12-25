---
layout: post
title: 자바스크립트에서 객체 상속하기
tags:
  - javascript
  - object-oriented programming
  - inheritance
  - 자바스크립트
  - 객체지향 프로그래밍
  - 상속
---
* 자바스크립트에는 클래스가 없다.
* 자바스크립트에서는 객체 간에 상속을 위해서 *prototype chaining* 방법을 사용한다.
* 아래 코드는 Square 생성자가 Rectangle 생성자를 상속한다.

```javascript
function Rectangle(width, height) {
    'use strict';
    this.width = width;
    this.height = height;
}

Rectangle.prototype = {
    getArea: function() {
        return this.width * this.height;
    },
    toString: function() {
        return "[Rectangle " + this.width + "x" + this.height + "]";
    }
}

// inherits from Rectangle
function Square(size) {
    'use strict';
    this.width = size;
    this.height = size;
}

Square.prototype = Object.create(Rectangle.prototype, {
    constructor: {
        configurable: true,
        enumerable: true,
        value: Square,
        writable: true
    }
});

Square.prototype.toString = function() {
    return "[Square " + this.width + "x" + this.height + "]";
};

var rect = new Rectangle(7,8);
console.log(rect.getArea());  // 56
console.log(rect.toString());  // "[Rectangle 7x8]"
console.log(rect instanceof Object); // true
console.log(rect instanceof Square);  // false
console.log(rect instanceof Rectangle);  // true

var square = new Square(6);
console.log(square.getArea());  // 36
console.log(square.toString());  // "[Square 6x6]"
console.log(square instanceof Object); // true
console.log(square instanceof Square);  // true
console.log(square instanceof Rectangle);  // true
```
