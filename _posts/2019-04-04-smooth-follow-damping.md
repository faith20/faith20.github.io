---
title: Frame Rate Independent Damping Using Lerp
categories: Game
tags: Game Dev
published: true
excerpt: Smooth Damping Math
---

Unity3D 로 작업하던 시기에 항상 혼동이 오는 문제 중의 하나가 Smooth Damping 이었습니다.  
나름 잘 설명된 블로그가 있어서 나중을 위해 정리해 보려고 합니다.

<!-- prettier-ignore -->
* content
{:toc}

## 1. Smooth Damping 이란?

현재 값 a 가 있고 타겟 값인 b 로 부드럽게 이동시키는 것이 목표인 문제 입니다.  
캐릭터(b)를 따라가는 카메라(a)의 좌표를 자연스럽게 변화시키는 예를 생각해 볼 수 있습니다.

## 2. Lerp 문제

보통 이 문제를 처리할 때 비율 r 과 lerp 를 이용한 선형 보간으로 매 프레임마다 아래처럼 호출합니다.

```c#
a = Mathf.Lerp( a, b, r );
```

하지만 이렇게 하면 프레임간의 시간차이에 영향을 받아 불안정한 움직임이 나옵니다.  
그래서 또 아래처럼 바꾸기도 합니다.

```c#
a = Mathf.Lerp( a, b, r * Time.deltaTime );
```

이 경우는 lerp 의 마지막 값이 1 을 넘어버리는 경우가 나올 수도 있기 때문에 역시나 답이 아닙니다.

## 3. 간단한 수식화

문제를 단순화 해서 a 를 0 으로 옮기는 방법을 생각해봅시다.  
a 의 초기 값을 10 이라 하고 반씩 줄여나간다고 가정을 합니다.

$$
\begin{align}
a(0) & = 10 \\
a(1) & = 5 \\
a(2) & = 2.5 \\
a(3) & = 1.25 \\
\end{align}
$$

그래프로 보면 a 값이 점차 0 에 수렴해 가는 것을 알 수 있습니다.
![Graph1]({{ site.baseurl }}/assets/images/damping/image-1.png)  
이 것을 다시 수식으로 적어보면

$$
a(t + 1) = \frac{a(t)}{2}
$$

절반이 아니라 범위가 0 ~ 1 인 r 의 비율을 사용한다고 하면

$$
\begin{align}
a(t + 1) & = a(t)r \\
a(t + 2) & = a(t + 1)r = a(t)r^2 \\
a(t + 3) & = a(t + 2)r = a(t)r^3 \\
\end{align}
$$

결국 다음과 같은 수식을 만들 수 있습니다.

$$
a(t + n) = a(t)r^n
$$

이 수식을 코드로 만들어 보면 아래와 같습니다.

```c#
// Smoothing rate dictates the proportion of source remaining after one second
//
public static float Damp(float source, float smoothing, float dt)
{
  return source * Mathf.Pow(smoothing, dt);
}

private void Update()
{
  a = Damp(a, 0.5f, Time.deltaTime);
}

// or

private void FixedUpdate()
{
  a = Damp(a, 0.5f, Time.fixedDeltaTime);
}
```

## 4. 타겟을 적용해보자

위에서는 b 를 0 으로 가정했고 a 가 실제 b 에 수렴하도록 하려면 그래프를 y 축으로 b 만큼 이동시켜 주면 됩니다.
![Graph2]({{ site.baseurl }}/assets/images/damping/image-2.png)

$$
\begin{align}
a(t + n) - b & = (a(t) - b)r^n \\
a(t + n) & = b + (a(t) - b)r^n \\
a(t + n) & = b - br^n + a(t)r^n \\
a(t + n) & = b(1 - r^n) + a(t)r^n \\
\end{align}
$$

이 수식은 처음에 사용하려고 했던 lerp 와 같습니다.

```c#
a(t + n) = Lerp(b, a(t), Pow(r, n))
```

그런데

```c#
Lerp(a, b, t) = Lerp(b, a, 1 - t)
```

이므로

```c#
a(t + n) = Lerp(a(t), b, 1 - Pow(r, n))
```

이렇게 표현 할 수 있습니다.  
그대로 코드로 만들어 보면 아래와 같습니다.

```c#
// Smoothing rate dictates the proportion of source remaining after one second
//
public static float Damp(float source, float target, float smoothing, float dt)
{
  return Mathf.Lerp(source, target, 1 - Mathf.Pow(smoothing, dt))
}
```

이대로 사용해도 충분히 자연스러운 움직임을 확인 할 수 있습니다.
smoothing 값은 0 ~ 1 사이만 허용됩니다.

## 5. Exponential Decay

위의 그래프를 다시한번 살펴보면 exponential decay (지수적 감쇠?) 함수인 것을 알 수가 있는데  
b 를 0 으로 가정했을 때의 수식으로 생각해보면

$$
\begin{align}
a(t + n) & = a(t)r^n \\
a(t + n) & = a(t)e^{-\lambda n} \\
r^n & = e^{-\lambda n} \\
r^n & = (e^{-\lambda})^n \\
r & = e^{-\lambda} \\
\lambda & = -\ln(r) \\
\end{align}
$$

따라서 아래와 같은 코드로 변형 할 수 있습니다.

```c#
public static float Damp(float a, float b, float lambda, float dt)
{
  return Mathf.Lerp(a, b, 1 - Mathf.Exp(-lambda * dt))
}
```

조정할 수 있는 변수가 위의 smoothing 에서 lambda 로 바뀐 것 뿐인 듯 하지만  
smoothing 은 0 ~ 1 사이의 값만 가능하고 lambda 는 0 ~ 무한대의 값이 가능하기때문에  
사용이 편리한 장점이 있습니다.

두가지 코드를 그래프로 확인해보면 동일한 결과를 볼 수 있습니다.
![Logo]({{ site.baseurl }}/assets/images/damping/image-5.png)

## Conclusion

결국 필요한 것은 마지막 한줄의 코드이지만  
구현과정을 이해하고 사용해야 나중에라도 다시 혼동하는 일이 없을 것 같습니다.

<br>
`출처`
[http://www.rorydriscoll.com/2016/03/07/frame-rate-independent-damping-using-lerp/](http://www.rorydriscoll.com/2016/03/07/frame-rate-independent-damping-using-lerp/)
