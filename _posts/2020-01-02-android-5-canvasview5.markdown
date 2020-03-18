---
layout: post
title:  "[Android] CanvasView 구현하기 (4) - Pinch-Zoom/Out 구현하기"
subtitle: "CanvasView/DrawView/안드로이드 그림판/캔버스 확대/"
date:   2020-01-02 19:24:23 +0900
categories: dev
tags : android
---

DrawView/CanvasView에 핀치줌(두 손가락으로 벌리고 좁히는 것)으로 캔버스를 확대하거나 축소하는 기능과, 두 손가락 드래그를 통해 캔버스를 이동시키는 기능을 구현했다.

일단 그 전에, 해결해야 할 문제가 있었다. 멀티 터치를 기존에 구현했던 캔버스에 적용하게 되면, 여러 멀티 터치 지점들의 좌표가 뒤얽히면서 화면 상에 표시된다. 이를 위해 일단 단일 터치일 경우만 화면에 그려지도록 다음과 같이 구현했다.

`MotionEvent.getPointerCount()` 메소드는 화면 상에 터치 이벤트가 발생하고 있는 지점의 개수를 반환한다. 이 메소드가 반환하는 숫자가 1일 경우에는 단일 터치 중인 것이고, 2개 이상일 경우엔 멀티 터치 중인 것이다.

일단 처음에는 단일 터치 중일 때만 화면 상에 그려지도록 했다.

{% highlight java %}
@Override
public boolean onTouch(View v, MotionEvent event) {
    ...
    if(event.getPointerCount() == 1){
        /* draw on canvas */
    }
    else{
        isMultitouching = true;
    }
}
{% endhighlight %}

위의 코드처럼 단일 터치일 경우만 화면 상에 그려지도록 구현했었다. 그러나 이렇게 하더라도 한 손가락으로 그리던 도중 다른 손가락으로 멀티 터치를 하고, 먼저 터치를 했던 손가락을 떼게 되면 마찬가지로 터치 좌표가 뒤얽히게 된다.

따라서 터치 이벤트를 발생시키는 `Pointer ID`를 구분해주고, ID가 동일한 터치 이벤트에 대해서만 화면에 그려지도록 구현해야 했다.

{% highlight java %}
@Override
public boolean onTouch(View v, MotionEvent event) {
    ...
    if(event.getPointerCount() == 1){
        /* draw on canvas */
        switch (event.getActionMasked()) {
            case MotionEvent.ACTION_UP:
            case MotionEvent.ACTION_MOVE:
                if(this.mActivePointerID == event.getPointerId(event.getActionIndex()) && !isMultiTouching) {
                    /* 최초 단일 터치 지점일 때와 멀티 터치 중이지 않을 때 진입 */
                    /* isMultiTouching 플래그는 단일 -> 멀티 -> 단일 터치로 상황이 변경될 때를 방지 */
                    /* draw on Canvas */
                }
                break;
            case MotionEvent.ACTION_DOWN:
                /* 최초 단일 터치 지점을 mActivePointerID로 저장 */
                this.mActivePointerID = event.getPointerId(0);
                this.isMultiTouching = false;
                break;
        }
        ...
    }
    else{
        isMultitouching = true;
    }
}
{% endhighlight %}

이를 통해서 멀티 터치를 할 때 화면 상에 좌표가 뒤얽혀 이상하게 그려지는 현상은 고칠 수 있었고, 여기서부터 Zoom in과 Zoom out을 구현할 수 있었다.

-----------

#### Canvas Scaling

Canvas에서 `Matrix` 이용해서 `translation`, `scaling`, `rotation`과 같은 화면 상의 왜곡을 주는 기능을 적용할 수 있다.

나는 여기서 캔버스를 두 손가락을 벌리는 제스처에서 확대가 되고, 좁히는 제스처에서 축소가 될 수 있도록 구현하고 싶었고`scaling`, 또 확대된 상태에서 두 손가락으로 드래그를 했을때 드래그 한 방향으로 캔버스가 이동하는`translation` 기능을 구현했다.

`ScaleGestureDetector.SimpleOnScaleGestureListener()`의 `onScale` 메소드를 override 하여 Pinch 이벤트를 처리할 수 있었다.

`public boolean onScale(ScaleGestureDetector detector)` 

`onScale` 메소드의 `detector`를 이용해 `scaleFactor`를 읽어올 수 있는데, 이 `scaleFactor`를 `onScale` 메소드가 호출될 때마다 누적시켜 곱해주었고, `canvas.scale()` 메소드에 누적된 `scaleFactor`를 넘겨주는 방식으로 확대/축소를 구현했었다.

확대/축소를 할 경우 원래 캔버스의 좌표계가, 터치 이벤트로 전달되는 좌표계와 달라져서 터치를 했을 경우 다른 위치에 그려지는 현상이 발생한다. 따라서 이를 위해 또 터치 이벤트의 좌표 값을 scale된 만큼 보정을 해줘야 했는데, 이는 다음과 같이 처리할 수 있었다.

{% highlight java %}
    @Override
    protected void onDraw(Canvas canvas) {
        ...
        canvas.getClicpBounds(mCanvasClipBounds);
        ...
    }
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        ...
        float touchX = motionEvent.getX() / mZoomFactor + mCanvasClipBounds.left;
        float touchY = motionEvent.getY() / mZoomFactor + mCanvasClipBounds.top;
        ...
    }
{% endhighlight %}

-----------

#### Canvas Scaling & Translation

그러나 위의 `canvas.scale` 방식으로 구헌하게 됐을 때는 내가 원하는 두 손가락으로 캔버스를 드래그 했을 때 좌표계가 따라 움직이는 기능은 구현하지 못했었다.

이 기능을 적용하기 위해 `Matrix`를 사용하였는데, **[StackOverflow]({{"https://stackoverflow.com/questions/19458094/canvas-zooming-in-shifting-and-scaling-on-android"}})** 여기를 참고해서 구현했었다.

{% highlight java %}

@Override
    ...
    this.mScaleDetector = new ScaleGestureDetector(this.mContext, new ScaleGestureDetector.SimpleOnScaleGestureListener(){
        @Override
        public boolean onScaleBegin(ScaleGestureDetector detector) {
            /* 최초 스케일 시작 지점을 저장 */
            lastFocusX = detector.getFocusX();
            lastFocusY = detector.getFocusY();
            return super.onScaleBegin(detector);
        }
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
    }
    this.mGestureDetector = new GestureDetector(this.mContext, new GestureDetector.SimpleOnGestureListener(){
        @Override
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            if(isMultiTouching) { /* onTouch에서 멀티 터치일 경우 true로 설정되도록 함 - 이전 코드 참고 */
                drawMatrix.postTranslate(-distanceX, -distanceY);
                invalidate();
                return true;
            }
            return super.onScroll(e1, e2, distanceX, distanceY);
        }
    });
{% endhighlight %}

이를 이용해서 `scale`, `translate` 모두 쉽게 구현할 수 있었지만, 터치 좌표를 변형된 캔버스에 보정시켜주는 것이 까다로웠다.

이전에 했던 방식대로 scaleFactor와 이동한 좌표들을 이용해서 임의로 보정을 해주더라도 조금씩 차이가 있었기 때문에 굉장히 까다로웠다.

그러던 중 **[StackOverflow]({{"https://stackoverflow.com/questions/9016230/convert-touched-value-to-points-based-on-matrix"}})** 이 글을 알게 되었고, 답변을 달아주신 분을 참고해서 딱 필요했던 좌표 보정을 할 수 있었다.

{% highlight java %}
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        ...
        Matrix invertMatrix = new Matrix();
        drawMatrix.invert(invertMatrix);
        float[] translated_xy = {event.getX(), event.getY()};
        invertMatrix.mapPoints(translated_xy);

        // translated_xy[0] : 변형된 Matrix에 따라 보정된 캔버스 상의 X 좌표
        // translated_xy[1] : 변형된 Matrix에 따라 보정된 캔버스 상의 Y 좌표

    }
{% endhighlight %}

------------------

![1]({{"assets/img/dev/android/5/1.gif" | absolute_url}})