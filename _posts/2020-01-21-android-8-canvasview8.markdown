---
layout: post
title:  "[Android] CanvasView 구현하기 (7) - 지우개 구현(2) - 캔버스 지우기"
subtitle: "Canvas 지우기"
date:   2020-01-21 16:44:44 +0900
categories: dev
tags : android
---

이전 포스팅에서 필기 앱들의 지우개 기능을 사용할 때 `Canvas` 위에 보여지는 작은 원을 보여줄 수 있도록 추가했었다.

이제 해당 원이 `Canvas` 위의 필기를 만나면 지워질 수 있도록 기능을 구현해봤다.

처음에 터치 영역에 대한 `bitmap`을 어떻게 하면 지울 수 있을지 고민했었는데, 생각보다 쉽게 답을 찾았다.

`Canvas`에 그림을 그릴 때 사용하는 `Paint` 클래스의 `Xfermode`를 활용하면 구현할 수 있었다.

`Canvas`에 그림을 그리면 `bitmap`으로 표현되는데, 이미 그림이 그려진 `bitmap`에 대하여 같은 좌표에 다시 그릴 경우, 어떻게 처리할지에 대한 논리를 정의할 수 있는데`(transfer mode)`, 이러한 기능을 `Xfermode`클래스가 제공한다.

{% highlight java %}
public Xfermode setXfermode (Xfermode xfermode)
{% endhighlight %}

`Xfermode`는 일반적으로 `PorterDuffXfermode` 클래스의 인스턴스를 전달받는데, 이 `PorterDuffXfermode` 클래스는 `PorterDuff.Mode`에 정의된 `enum` 타입을 전달받는다.

`PorterDuff`에서 지원하는 `Mode`는 `ADD`, `CLEAR`, `DARKEN`, `DST` 등이 있는데 이는 docs에 예제 그림과 함께 잘 나와있다.

### [developers]({{"https://developer.android.com/reference/android/graphics/PorterDuff.Mode"}})

---
결과적으로 다음과 같은 코드로 쉽게 구현할 수 있었다.

{% highlight java %}
mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.CLEAR)); /* Eraser mode */
mPaint.setXfermode(null); /* Pen mode */
{% endhighlight %}



![1]({{"assets/img/dev/android/8/1.gif" | absolute_url}})
