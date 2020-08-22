---
title: JavaScript Methods
categories: JavaScript
tags: Web Dev
excerpt:
---

<!-- prettier-ignore -->
* content
{:toc}

### 전통적인 for-loop 에서 벗어나기

js코딩을 하다보면 자꾸만 c에서 하던 for-loop 를 똑같이 사용하게 되는데  
깔끔한 코드와 조금이라도 성능 향상을 노려보기 위해 새로운 방법들을 익혀야 할 필요가 있습니다.

## 1. Spread operator

```js
const favoriteFood = ['Pizza', 'Fries', 'Swedish-meatballs'];
console.log(...favoriteFood);
// Pizza Fries Swedish-meatballs
```

- 배열의 내용을 간단히 빠르게 보여줄때
- 빠르고 직관적으로 인수를 전달하는 방법
- 배열 뿐 아니라 Object Literal 도 가능

## 2. for…of iterator

```js
const toolBox = ['Hammer', 'Screwdriver', 'Ruler'];
for (const item of toolBox) {
  console.log(item);
}
// Hammer
// Screwdriver
// Ruler
```

- for...in 의 경우는 key 값을 가져오지만 순서가 보장되지 않고, 엉뚱한 property 를 가져올 수 있기 때문에 hasOwnProperty 와 함께 사용

## 3. includes()

```js
const garage = ['BMW', 'AUDI', 'VOLVO'];
const findCar = garage.includes('BMW');
console.log(findCar);
// true
```

- 대소문자 주의

## 4. some()

```js
const age = [12, 14, 18];
age.some((person) => person >= 18);
// Output: true
```

- 하나라도 만족하면 true

## 5. every()

```js
const age = [15, 20, 19];
age.every((person) => person >= 18);
// Output: false
```

- 모두 만족해야 true

## 6. filter()

```js
const prices = [25, 30, 15, 55, 40, 10];
prices.filter((price) => price >= 30);
// [30, 55, 40]
```

- 새로운 배열 생성

## 7. map()

```js
const productPriceList = [200, 350, 1500, 5000];
productPriceList.map((item) => item * 0.75);
// [150, 262.5, 1125, 3750]
```

- filter() 와 다른 점은 값을 바꿀 수 있다는 것

## 8. reduce()

```js
const weeklyExpenses = [200, 350, 1500, 5000, 450, 680, 350];
weeklyExpenses.reduce((first, last) => first + last);
// 8530
```

- 배열 값으로 계산할 때 활용

<br>
`출처`
[https://medium.freecodecamp.org/7-javascript-methods-that-will-boost-your-skills-in-less-than-8-minutes-4cc4c3dca03f](https://medium.freecodecamp.org/7-javascript-methods-that-will-boost-your-skills-in-less-than-8-minutes-4cc4c3dca03f)
