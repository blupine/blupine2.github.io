---
layout: post
title:  "[Android] CanvasView 구현하기 (2) - 선을 부드럽게 처리하기"
subtitle: "CanvasView/DrawView/안드로이드 그림판"
date:   2019-12-22 23:52:23 +0900
categories: dev
tags : android
---

이전 포스팅에서 Canvas와 Path, Paint를 활용해서 기본적인 그림판을 구현했었다.

Touch Event가 발생할 때마다 좌표 값을 읽어와서 Path를 이어주는 방식으로 구현하니 다음과 같이 직선적으로 이어진다는 문제가 있었다.

![1]({{"assets/img/dev/android/3/1.png" | absolute_url}})

빠르게 획을 그리게 되면 중간에 터치 이벤트 사이에 많은 공간이 생기게 되고 그만큼 직선적으로 표현되기 때문에 이런 문제가 발생한다.

처음에 책이나 스택오버플로우를 뒤져봐도 `Path`를 이어줄 때 `quadTo`로 이어주게 되면 곡선처럼 표현된다는 해결 방법이 있었는데, 이는 곡선의 꼭짓점?을 직접 구해서 전달해줘야 했고, 이를 또 직접 계산을 해서 곡선을 그려준다고 해도 상당히 부자연스러웠다.

여러 삽질을 하던 중 안드로이드에서 터치 이벤트는 `batch`의 형태로 들어온다는 것을 알게 되었다.

**[SignatureView]({{"https://developer.squareup.com/blog/smooth-signatures"}})**

이 말은 즉, `onTouch` 메소드로 들어오는 터치 이벤트는 바로 이전에 발생했던 터치 이벤트부터, 지금 발생한 터치 이벤트 가지의 값을 모두 갖고 포함하고 있는 것이다.

- `getHistorySize()`
- `getHistoricalX()`
- `getHistoricalY()`

이 세 가지 메소드를 이용해서 직전에 발생했던 터치 이벤트부터, 현재 발생한 터치 이벤트 사이에 발생한 터치들의 좌표 값을 알아낼 수 있다.

이렇게 알아낸 좌표 값들에 대해서도 일일이 `Path.lineTo()`를 이용해 이어준다면 좀 더 조밀하게 곡선이 표현될 수 있을 것이다.

<br>

![2]({{"assets/img/dev/android/3/2.png" | absolute_url}})

위와 같이 위의 메소드들을 이용해서 이전의 터치 이벤트 좌표 값을 읽어올 수 있었고, 추가적으로 Path를 이어줄 수 있었다.

결과적으로 완성된 화면은 다음과 같이 좀 더 부드러워질 수 있었다.

<br>


![3]({{"assets/img/dev/android/3/3.png" | absolute_url}})

다음 포스팅에선 위의 `SignatureView`를 구현하신 블로거 분의 글을 참고해서 필압과 획 속도?를 캔버스에 반영할 수 있도록 구현해보자..