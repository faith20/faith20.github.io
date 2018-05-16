---
layout: post
title: "JavaScript Methods"
categories: JavaScript
tags: Web Dev
---

* content
{:toc}

[`출처`](https://medium.freecodecamp.org/7-javascript-methods-that-will-boost-your-skills-in-less-than-8-minutes-4cc4c3dca03f)

## 전통적인 for-loop 에서 벗어나기
```js
(let i; i < value.length; i++ )
```
js코딩을 하다보면 자꾸만 c에서 하던 for-loop 를 똑같이 사용하게 되는데  
깔끔한 코드와 조금이라도 성능 향상을 노려보기 위해 새로운 방법들을 익혀야 할 필요가 있습니다.

1. Spread operator
2. for…of iterator
3. includes()
4. some()
5. every()
6. filter()
7. map()
8. reduce()

## 1. Spread operator
```js
const favoriteFood = ['Pizza', 'Fries', 'Swedish-meatballs'];
console.log(...favoriteFood);
// Pizza Fries Swedish-meatballs
```
### 이럴 때 사용
* 배열의 내용을 간단히 빠르게 보여줄때
* 빠르고 직관적으로 인수를 전달하는 방법
* 배열 뿐 아니라 Object 도 가능

## 2. for…of iterator
```js
const toolBox = ['Hammer', 'Screwdriver', 'Ruler'];
for(const item of toolBox) {
  console.log(item);
}
// Hammer
// Screwdriver
// Ruler
```

## 3. includes()
```js
const garage = ['BMW', 'AUDI', 'VOLVO'];
const findCar = garage.includes('BMW');
console.log(findCar);
// true
```
## 4. some()
```js
const age = [12, 14, 18];
age.some(person => person >= 18);
// Output: true
```
## 5. every()
```js
const age = [15, 20, 19];
age.every(person => person >= 18);
// Output: false
```
## 6. filter()
```js
const prices = [25, 30, 15, 55, 40, 10];
prices.filter(price => price >= 30);
// [30, 55, 40]
```
## 7. map()
```js
const productPriceList = [200, 350, 1500, 5000];
productPriceList.map(item => item * 0.75);
// [150, 262.5, 1125, 3750]
```
## 8. reduce()
```js
const weeklyExpenses = [200, 350, 1500, 5000, 450, 680, 350];
weeklyExpenses.reduce((first, last) => first + last);
// 8530
```
