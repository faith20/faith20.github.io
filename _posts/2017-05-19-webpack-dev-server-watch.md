---
title: 'webpack-dev-server watch'
categories: JavaScript
tags: Web Dev
excerpt:
---

<!-- prettier-ignore -->
* content
{:toc}

작업하던 코드를 TypeScript로 변환 하면서  
Gulp를 쓰지 않고 Webpack Plugin만 사용하는 것으로 구조를 변경했습니다.

## `TypeScript` & `Webpack`

그런데 webpack-dev-server를 띄워보니
코드 수정한게 자동으로 반영되지 않는 문제가 발생하네요.

분명 설명에는 webpack-dev-server가 webpack watch 모드로 동작한다고 본 것 같은데
메모리에만 존재하고 실제 파일로는 저장되지 않기때문인가 싶어 이것저것 바꿔봐도 안되더군요.

그러다가 [`Document`](https://webpack.github.io/docs/webpack-dev-server.html)의 댓글을 늦게 발견하고 해결했습니다.

## `webpack.config.js`

원문

> This was tough to track down, but I did.  
> Webpack is very picky about the values of the path and filename properties of the output config property.  
> My main .js file is public/build/bundle.js.  
> Hot reload did **not** work with
>
> ```js
> output: { path: __dirname, filename: 'public/build/bundle.js' }
> ```
>
> It also did **not** work with
>
> ```js
> output: { path: __dirname + '/public/build', filename: 'bundle.js' }
> ```
>
> However, it **DOES WORK** with
>
> ```js
> output: { path: __dirname + '/public', filename: 'build/bundle.js' }
> ```

참고로 제가 사용했던 설정은

```js
output: { filename: "bundle.js", path: path.resolve(__dirname, "dist", "js") }
```

였는데

```js
output: { filename: "js/bundle.js", path: path.resolve(__dirname, "dist") }
```

로 변경하니 바로 해결되었습니다.

정확히는 모르겠지만 output path 를 실제 working path 로 지정해야 하는 것 같네요.  
엄한 걸로 삽질좀 그만해야 하는데.. 하아..
