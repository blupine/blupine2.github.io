---
layout: post
title:  "[Android] CanvasView 구현하기 (3) - 필압 구현"
subtitle: "CanvasView/DrawView/안드로이드 그림판"
date:   2019-12-28 11:52:23 +0900
categories: dev
tags : android
---

**[SignatureView]({{"https://developer.squareup.com/blog/smooth-signatures"}})** 글을 보고 구현 중인 DrawView에 베지어 곡선을 적용했었다.

그러나 생각했던 것 보다 곡선 부분이 극적으로 보여지지 않았고, 곡선을 계산하는 데 들어가는 오버헤드를 고려했을 때 굳이 필요할까 싶어서 일단 구현을 미뤄두기로 했다.

그 대신 필압에 따라 펜의 굵기가 달라질 수 있도록 기능을 구현했다.

Touch 이벤트에 대한 압력 값은 `MotionEvent.getPressure()` 메소드를 통해 얻어올 수 있다.


한 획`(stroke)` 을 이루는 path 정보를 점과 점 사이를 이어주는 path들의 리스트 형태로 유지하고, 리스트의 아이템 별로 필압에 따른 `width`를 설정해준다.

![1]({{"assets/img/dev/android/4/1.png" | absolute_url}})

Path 클래스 인스턴스에 넓이 정보`(width)`를 포함시킬 수 있도록 Path를 상속받는 `StrokeWidth` 클래스를 작성해줬고, 이 `StrokeWidth` 인스턴스를 아이템으로 가지는 리스트를 선언해준다.

![2]({{"assets/img/dev/android/4/2.png" | absolute_url}})

`MotionEvent.ACTION_DOWN` 이벤트에 호출될 때 호출되는 메소드로, 최초 path 정보를 가지는 `mPath`의 초기 좌표를 설정해준다.

![3]({{"assets/img/dev/android/4/3.png" | absolute_url}})

이후 `MotionEvent.ACTION_MOVE` 이벤트에 호출되는 메소드로 `quadTo`를 이용해서 점과 점 사이를 이어주는 곡선을 만들어주고, 압력 값에 따른 펜 굵기를 곱셈을 통해 임의로 결정했다.

이후 해당 굵기`(mPaint.getStrokeWidth() * p)`를 기준으로 페인트 인스턴스의 넓이를 설정해주고 캔버스에 그려준다.

이후 이전의 페인트 인스턴스의 넓이를 복원해준다.

<br>
<br>

베지어 곡선을 구현하고, 부드럽게 처리하는 과정에서 많은 시간이 걸렸지만, **[INKredible]({{"https://play.google.com/store/apps/details?id=com.viettran.INKredible&hl=ko"}})**이라는 상용 앱에서도 베지어 곡선을 통한 필기 보정을 했다는 점에서, 구현만 잘 해준다면 충분히 효과를 볼 수 있을 것 같다.

다른 기능들을 구현한 이후 다시 적용해보도록 하겠다.
