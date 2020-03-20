---
layout: post
title:  "[Android] CanvasView 구현하기 (5) - Pinch-Zoom/Out 구현하기(2)"
subtitle: "CanvasView/DrawView/안드로이드 그림판/캔버스 확대/"
date:   2020-01-05 22:24:23 +0900
categories: dev
tags : android
---

앞선 포스팅에 캔버스에 핀치줌, 핀치아웃 기능을 적용했었다.

그러나 캔버스의 크기를 벗어나는 영역에 대해서도 줌이 되는데, 이부분에 대해 좌측 상단 캔버스의 모서리 부분만 고정을 하고싶었다.

일단 뭐가 문제가 되는지 보면

![1]({{"assets/img/dev/android/6/1.gif" | absolute_url}})

축소를 할 때 캔버스의 크기를 벗어난 영역가지 보이게 축소가 되어서 캔버스 영역을 벗어나는 것이다.

나중에 캔버스 크기를 더 키울 계획이었지만, 영역을 벗어난 부분까지 축소가 되어서는 안됐기 때문에 좌측 상단의 캔버스 영역은 고정되도록 구현했다.

우선 이전에 캔버스를 확대, 축소, 좌표 변환을 했던 코드는 다음과 같다.

{% highlight java %}
    @Override
    public boolean onScale(ScaleGestureDetector detector) {
        Matrix transformationMatrix = new Matrix();
        float focusX = detector.getFocusX();
        float focusY = detector.getFocusY();
        
        transformationMatrix.postTranslate(-focusX, -focusY);
        transformationMatrix.postScale(detector.getScaleFactor(), detector.getScaleFactor());
        
        /* 최초 스케일 시작 지점으로부터 얼마나 이동했는지 측정 */
        float focusShiftX = focusX - lastFocusX;
        float focusShiftY = focusY - lastFocusY;

        transformationMatrix.postTranslate(focusX + focusShiftX, focusY + focusShiftY); /* Matrix 이동 */
        
        drawMatrix.postConcat(transformationMatrix);
        lastFocusX = focusX;
        lastFocusY = focusY;
        invalidate();
        return true;
    }
{% endhighlight %}

여기서 좌측 상단이 캔버스 영역 밖으로 벗어나지 않도록 고정하기 위해선, 우선 스케일 이후에 변환이 완료됐을 때의 좌표가 음수인지를 확인해야 한다. 변환된 X, Y의 좌표가 각각 음수일 경우 캔버스의 좌측 또는 상단이 캔버스 영역을 벗어난 것이기 때문이다.

위 변환 코드에서 원점`(0, 0)`이 어느 좌표로 전환되는지 계산하면 다음과 같다.

{% highlight java %}
    float afterX = -1 * focusX * scaleFactor + focusX + focusShiftX;
    float afterX = -1 * focusY * scaleFactor + focusY + focusShiftY;
{% endhighlight %}

해당 변환 식에 기존 `Matrix`의 X, Y를 더해주면 최종적으로 변환이 된 원점의 X, Y 좌표를 알 수 있다.

이후 해당 X, Y가 음수일 경우엔 좌표 이동`translate`는 해주지 않는 것으로 좌상단 좌표를 고정시킬 수 있었다.

--------
#### 최종 코드

{% highlight java %}
            @Override
            public boolean onScale(ScaleGestureDetector detector) {
                final float scaleFactor = detector.getScaleFactor();

                float[] values = new float[9];
                drawMatrix.getValues(values);   /* 현재 Matrix로부터 원점 좌표를 얻어옴 */

                Matrix transformationMatrix = new Matrix();
                float focusX = detector.getFocusX();
                float focusY = detector.getFocusY();

                float focusShiftX = focusX - lastFocusX;
                float focusShiftY = focusY - lastFocusY;

                /* after translated coordinate */
                float afterX = values[Matrix.MTRANS_X] + (-1 * focusX * scaleFactor + focusX + focusShiftX); /* Scale & Translate 이후 X 좌표 */
                float afterY = values[Matrix.MTRANS_Y] + (-1 * focusY * scaleFactor + focusY + focusShiftY); /* Scale & Translate 이후 Y 좌표 */

                /* translation coordinate must be 0 if translated coordinate is larger than 0 : fixing top-left coordinate of canvas */
                transformationMatrix.postTranslate(afterX < 0 ? -focusX : 0, afterY < 0 ? -focusY : 0);

                transformationMatrix.postScale(scaleFactor, scaleFactor);

                mScaleFactor *= scaleFactor;

                /* translation coordinate must be 0 if translated coordinate is larger than 0 : fixing top-left coordinate of canvas */
                transformationMatrix.postTranslate(afterX < 0 ? focusX + focusShiftX : 0, afterY < 0 ? focusY + focusShiftY : 0);

                drawMatrix.postConcat(transformationMatrix);

                lastFocusX = focusX;
                lastFocusY = focusY;
                invalidate();
                return true;
            }
{% endhighlight %}

----------------------

![2]({{"assets/img/dev/android/6/2.gif" | absolute_url}})