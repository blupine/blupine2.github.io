---
layout: post
title:  "[Android] View의 넓이, 높이 구하기"
subtitle: ""
date:   2019-11-23 17:37:23 +0900
categories: dev
tags : android
---

커스텀 뷰를 만들어 개인 프로젝트를 하는 도중에 뷰의 크기를 측정해줄 필요가 있었다.

`getWidth()`, `getHeight()` 메소드를 이용해서 뷰의 크기를 측정하려 했으나 사이즈가 0으로 나온다.

이유를 찾아보니 다음과 같은 글을 찾아볼 수 있었다.

### [stackoverflow]({{"https://stackoverflow.com/questions/3591784/views-getwidth-and-getheight-returns-0/15578844"}})

내용을 요약하자면,

화면에 UI가 그려지기 전에 `getWidth()`, `getHeight()` 메소드를 호출할 경우 사이즈가 0으로 나오는 문제가 있다.

따라서 화면의 UI가 그려진 이후에 해당 메소드들을 호출해야 하며, 이는 `ViewTreeObserver`의 리스너를 등록해서 

UI가 그려지는 시점에 특정 작업을 처리할 수 있다.

`ViewTreeObserver`는 view tree의 변화가 있을 때 리스너를 등록해서 작업을 처리할 수 있다. 

그중에서 내가 필요한 리스너는 view가 다 그려진 시점에 호출되는 `OnGlobalLayoutListener`이다.w

#### 리스너 등록

{% highlight java %}
getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
            @Override
            public void onGlobalLayout() {
                Log.d(TAG, "initCanvasView : (width : " + getWidth() + ", height : " + getHeight() + ")");
                getViewTreeObserver().removeOnGlobalLayoutListener(this);
            }
        });
{% endhighlight %}

그러나 단순히 리스너만 등록해두면 레이아웃이 변할 때마다 해당 메소드가 호출될 수 있기 때문에,

계속해서 레이아웃의 변화를 감지해야 하는 작업이 아닌 이상 위 코드 처럼 등록한 리스너를 해제해줘야 한다.
