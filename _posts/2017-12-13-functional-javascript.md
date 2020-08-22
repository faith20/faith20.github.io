---
title: Functional JavaScript
categories: JavaScript
tags: Web Dev
excerpt:
---

<!-- prettier-ignore -->
* content
{:toc}

## 1. No more for loop

```js
const cats = [
  { name: 'Mojo', months: 84 },
  { name: 'Mao-Mao', months: 34 },
  { name: 'Waffles', months: 4 },
  { name: 'Pickles', months: 6 }
];

var kittens = [];
// typical poorly written `for loop`
for (var i = 0; i < cats.length; i++) {
  if (cats[i].months < 7) {
    kittens.push(cats[i].name);
  }
}
console.log(kittens);
```

1. if 구문을 function 으로 변경
2. cat Object 에서 name 을 가져오는 function 생성
3. filter 와 map 을 이용하여 새로운 배열 생성

```js
const isKitten = (cat) => cat.months < 7;
const getName = (cat) => cat.name;
const kittens = cats.filter(isKitten).map(getName);
```

## 2. Objects 의 method 추출하기. map, filter and reduce

```js
const map = (f) => (x) => Array.prototype.map.call(x, f);
const value = 'Kitty Cat';

value.map(doSomething);
// => Uncaught TypeError: items.map is not a function
map(doSomething)(value);
// => ['K', 'i', 't', 't', 'y', ' ', 'C', 'a', 't']
```
