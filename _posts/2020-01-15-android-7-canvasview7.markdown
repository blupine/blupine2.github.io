---
layout: post
title:  "[Android] CanvasView 구현하기 (6) - 지우개 구현(1)"
subtitle: "FrameLayout에 ImageView 추가하기/"
date:   2020-01-15 23:11:23 +0900
categories: dev
tags : android
---

기본적인 CanvasView의 확대/축소 기능과 필기가 가능한 기능을 구현했으니 이제 지우개 기능을 구현했다.

필기 앱들을 살펴봤을 때 지우개 기능을 사용할 땐 화면에 지우는 영역을 확인할 수 있도록 원이 그려지는 것을 찾아볼 수 있다.(e.g. samsung note)

따라서 일단 터치했을 때 작은 원이 화면에 표시될 수 있도록 `ImageView`를 이용해서 구현해봤다. 

최초 `CanvasView`를 구상했을 때 이렇게 뷰 위에 여러 다른 뷰들이 보여질 필요가 있을 것 같아서 `FrameLayout`으로 구현했었는데, 따라서 `ImageView`를 그 위에 추가해주는 것이 가능했다.

일단 작은 원을 표현할 수 있는 `vector resource`를 추가해준다.

{% highlight xml %}
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="24dp"
        android:height="24dp"
        android:viewportWidth="24.0"
        android:viewportHeight="24.0">
    <path
        android:fillColor="#FFFFFFFF"
        android:strokeColor="#FF000000"
        android:pathData="M12,2C6.48,2 2,6.48 2,12s4.48,10 10,10 10,-4.48 10,-10S17.52,2 12,2z"/>
</vector>
{% endhighlight %}

원의 경계를 식별할 수 있도록 `strokeColor`를 검은색`(#FF000000)`으로 설정했다.


{% highlight java %}
public class CanvasView extends FrameLayout implements View.OnTouchListener {
    ...
    private int eraserSize = 50;
    private ImageView onEraserIcon;
    FrameLayout.LayoutParams eraserLayout = new LayoutParams(eraserSize, eraserSize);
    ...

    private void initCanvasView(){
        ...
        this.onEraserIcon = new ImageView(mContext);
        this.onEraserIcon.setImageResource(R.drawable.ic_oneraser_black_24dp);
        this.onEraserIcon.setLayoutParams(eraserLayout);
        this.onEraserIcon.setVisibility(INVISIBLE); /* 지우개 상태에서 화면을 터치할 경우에만 VISIBLE로 설정함 */

        this.addView(this.onEraserIcon);            /* CanvasView 레이아웃에 ImageView를 추가해줌 */
        ...
    }
    
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        ...
        switch (event.getActionMasked()) {
            case MotionEvent.ACTION_UP:
                if (this.penMode == PenMode.ERASER) {
                    onEraserIcon.setVisibility(INVISIBLE);      /* 지우개 사용 중 화면에서 손을 뗐을 경우 지우개가 사라지도록 함 */
                    break;
                }
                /* 지우개가 아닐 경우 ACTION_MOVE와 동일하게 처리 */
            case MotionEvent.ACTION_MOVE:
                ...
                if (penMode == PenMode.ERASER) {
                    onEraserIcon.setX(event.getX() - eraserSize / 2f);  /* ImageView의 정중앙이 터치 이벤트에 위치하도록 좌표 조절 */
                    onEraserIcon.setY(event.getY() - eraserSize / 2f);  /* ImageView의 정중앙이 터치 이벤트에 위치하도록 좌표 조절 */
                    /* erase current position of canvas */
                }
                ...
                break;
            case MotionEvent.ACTION_DOWN:
                ...
                if (this.penMode == PenMode.ERASER) {
                    onEraserIcon.setX(event.getX() - eraserSize / 2f);
                    onEraserIcon.setY(event.getY() - eraserSize / 2f);
                    onEraserIcon.setVisibility(VISIBLE);        /* 지우개 사용 중 화면에 손을 터치했을 때 보여지도록 함 */
                    /* erase current position of canvas */    
                    break;
                }
                ...
}
{% endhighlight %}

-------

![1]({{"assets/img/dev/android/7/1.gif" | absolute_url}})