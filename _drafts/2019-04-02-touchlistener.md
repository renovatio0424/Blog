---
title: "[Android] 뷰 뒤에 있는 버튼 클릭 막기"

categories:
  - Android
  - UI

author: Reno

toc: true
toc_label: "차례"
toc_icon: "list"
---

## 이 글을 쓰게 된 이유

 이번에 개발하면서 반투명한 프래그먼트(A)를 액티비티(B) 위에 띄웠는데 액티비티에 있던 버튼이 먹는 현상이 있었습니다.  
 프래그먼트를 띄워진 상태를 저장해서 클릭을 막는 방법도 있었지만 왜 뒤에 있는 버튼이 클릭을 먹는 건지 궁금해서 테스트해보기 시작했습니다.  


## View를 터치했을 때 일어나는 일들

 *부모뷰와 자식뷰만 있는 경우*에  **TouchListener**가 어떻게 동작하는 지를 검색해보고 테스트 해본 결과 큰 흐름은 다음과 같습니다.

 Activity.dispatchTouchEvent() ->
 View.dispatchTouchEvent() -> View.onTouchEvent() -> View.DispatchTouchEvent() ->
 Activity.onTouchEvent() -> Activity.dispatchTouchEvent()

사진1,2,3

위의 흐름을 설명 또는 요약

## 중간에 ViewGroup이 있을 경우
다른점: ViewGroup이 중간에서 TouchEvent를 가로칠수있다.
1. 자식뷰에게 이벤트 전달 안하는 경우 (onIterceptTouchEvent)
2. 부모뷰에게 이벤트 가로채지 않도록 요청(requestDisallowInterceptTouchEvent)

사용 예제

## 결론
>뷰들의 터치 이벤트들은 터치한 뷰에게만 전달 되는것이 아니라
>부모 뷰로부터 이벤트가 발생해서 자식뷰까지 발생하고 다시 터치 이벤트를 부모 뷰에게 전달한다
