---
layout: post
title:  "[Android] CanvasView 구현하기 (1) - 기본적인 그림판 구현"
subtitle: "CanvasView/DrawView/안드로이드 그림판"
date:   2019-12-17 23:52:23 +0900
categories: dev
tags : android
---

안드로이드 CanvasView 또는 DrawView라고도 부르는 Custom view를 구현하고 있다.

기본적으로 canvas, path, bitmap을 활용해서 구현할 수 있다.

단순히 canvas만 담을 뷰를 만들고 끝마칠 것이 아니라 추후 color picker나 penstyle picker같은 추가 기능을 위해서는 여러 view를 더 보여줄 필요가 있을 것 같아서 FrameLayout을 상속받는 CustomLayout 형태로 구현했다.

![1]({{"assets/img/dev/android/2/1.png" | absolute_url}})

FrameLayout을 상속받도록 하였고 Touch 이벤트 처리를 위해 `onTouch` 리스너를 구현하자.

Canvas는 쉽게 도화지라고 생각하면 된다. `drawPath`, `drawLine`, `drawBitmap` 등의 멤버 메소드를 호출해서 그릴 수 있다.

Paint는 canvas에 그릴 색, 굵기 등을 정의하는 것이고 쉽게 팬, 붓 정도로 생각할 수 있을 것 같다.

Bitmap은 이번 포스팅에선 사용하지 않았으나 canvas의 배경 등을 설정하기 위해 사용할 수 있고, 효율적인 drawing을 위해선 활용해야 할 것 같다.

`ArrayList`로 DrawInfo 인스턴스를 history라는 이름으로 선언해뒀는데, 이는 draw 이벤트를 `undo`, `redo` 할 때 활용하기 위해 구현했다.

사용자가 그리는 획 단위를 리스트의 형태로 구현해서, `undo` 할 때는 포인터를 감소, `redo`할 때는 포인터를 증가시키는 방법으로 쉽게 구현할 수 있다.

<br>

![2]({{"assets/img/dev/android/2/2.png" | absolute_url}})

다음으로 커스텀 레이아웃을 초기화해주는 코드를 작성해준다.

첫번째 줄의 `this.setWillNotDraw(false);` 이 코드가 상당히 중요한데, 이 코드 때문에 삽질을 좀 많이 했다.

얼추 코드 작성을 끝마치고 실행을 했는데 처음 view가 그려질 때나, drawing 이벤트에 따라 화면이 갱신될 때 호출되어야 하는 `onDraw` 메소드가 전혀 호출되지 않는 문제가 있었다. 

**[Android Custom Layout - onDraw() never gets called]({{"https://stackoverflow.com/questions/13056331/android-custom-layout-ondraw-never-gets-called"}})**

Layout들은 ViewGroup을 상속받는데, 이런 ViewGroup의 객체들에 대해선 `onDraw`가 호출되지 않는다는 말인데.. 음..

아무튼 `this.setWillNotDraw(false);` 이 코드를 통해서 `onDraw`가 정상적으로 호출되게 만들 수 있었다. 

Canvas의 크기를 설정해주기 위해서 View의 크기를 받아와야 했는데, 앞서 작성했던 포스팅이 이 부분에서 만들어졌다.

**[View의 크기 알아내기]({{"https://blupine.github.io/dev/2019/11/23/android-view넓이구하기/"}})**

<br>

![3]({{"assets/img/dev/android/2/3.png" | absolute_url}})

`onTouch` 메소드이다.

`ACTION_DOWN`, `ACTION_MOVE`, `ACTION_UP` 이벤트에 대해 처리할 수 있도록 이처럼 작성했다.

아래 부분에 x, y 좌표를 이용해서 일부 화면만 갱신할 수 있도록 `invalidate(left, top, right, bottom)`을 이용했다.

<br>

![4]({{"assets/img/dev/android/2/4.png" | absolute_url}})

`ACTION_DOWN`, `ACTION_MOVE` 이벤트는 위와 같이 처리해줬다.

`ACTION_DOWN` 이벤트는 새로운 획을 그릴 때 발생하므로 새로운 `DrawInfo` 객체를 생성해서 `history` 리스트에 넣고 포인터를 증가시켜준다.

`ACTION_MOVE` 이벤트는 가장 마지막에 생성된 `history`를 참조해서 path 정보를 이어준다.

<br>

![5]({{"assets/img/dev/android/2/5.png" | absolute_url}})

`onDraw` 메소드는 위와 같이 구현했다.

`historyPointer`를 이용하여 가장 최근의 획 까지 그리기 정보를 가져온 다음에 캔버스에 그려준다.

그러나 이처럼 구현하니 획을 곡선의 형태로 빠르게 그릴 때 곡선이 직선적으로 표현된다는 문제점이 있다. (부드럽지 못함)

이부분에 대해서 다음 포스팅에서 해결해보도록 하겠다.

<br>

![6]({{"assets/img/dev/android/2/6.png" | absolute_url}})

마지막으로 `DrawInfo` 클래스는 위와 같이 구현했다.

해당 클래스는 한 획이 그려질 때마다 인스턴스화 되며, 획에 대한 path 정보와, paint 정보를 가지도록 했다.

현재 구현은 프로토타입 수준이기 때문에 여러 부분을 신경쓰지 못했고, paint 객체에 대한 deep copy가 아닌 점이 문제가 될 수도 있기 떄문에 추후 반영해야 한다.

