---
layout: post
title: Unity UI 최적화
date: 2021-04-05 09:29:20 +0700
modified: 2021-04-05 21:49:47 +07:00
categories: Blog
tags: [TIL, Unity]
description: UI optimization in Unity
---

# 1. Canvas 나누기

Unity UI는 기본적으로 Canvas** 안에 UI Component가 들어가고 **Draw Call**을 발생 시켜 최종적으로 화면에 보이게 된다. Canvas는 UI Component에 변형이 생기면 갱신을 시키게 되는데 문제는 Canvas 전체를 갱신시킨다.\
갱신할 때 Draw Call을 줄이기 위해 **Batching**을 최대한 하게 되는데 Batching은 연산이 비싸서 꼭 필요할 때만하는 것이 좋다. 불필요한 Canvas 전체의 갱신을 막기 위해선 Canvas를 **Static**과 **Dynamic**으로 나눠야 한다.

<figure>
<img src="/uiOptimization/UI_Seperation.png" alt="separate ui">
<figcaption>Fig 1. Divide UI Static/Dynamic.</figcaption>
</figure>

Canvas를 나누게 되면 각각의 Canvas에서 자신의 Batching만을 하게된다. 위에 사진처럼 **Canvas Nesting**도 가능하다.

**Draw Call** : CPU가 GPU에게 화면에 오브젝트를 그리라고 요청하는 것. 많아지면 디바이스에 따라 프레임 저하가 나타난다.\
**Batching** : 동일한 메테리얼을 공유하는 오브젝트들을 하나의 Draw Call로 묶어서 처리하는 작업.

# 2. Raycast Target 사용 해제

Raycast Target을 사용 안하는것 만으로 매프레임 Graphic Raycaster의 체크로 인한 연산을 줄일 수 있다.\
Static UI Canvas에는 Graphic Raycaster를 추가하지 않는 것도 중요하다.

<figure>
<img src="/uiOptimization/raycastTarget.png" alt="raycast target">
<figcaption>Fig 2. Raycast Target.</figcaption>
</figure>

# 3. Camera.main 사용을 피하기

World Space Canvas는 **Interaction Event**가 어느 카메라에서 오는지 알아야 한다. <kbd>Event Camera</kbd> 필드를 비워두면 `Camera.main`을 사용하게 된다. `Camera.main`은 게임오브젝트를 태그를 통해 찾는것이기 때문에 매프레임 호출되는 것은 좋지 않다. (2020.01 이상 버전에서는 더 이상 태그를 통해 찾지 않는다)
따라서 항상 <kbd>Event Camera</kbd>를 참조해 주는것이 좋다.

<figure>
<img src="/uiOptimization/worldSpaceCanvas.png" alt="world space canvas">
<figcaption>Fig 3. World Space Canvas.</figcaption>
</figure>

# 4. Layout Group 최대한 피하기

**Layout Group**은 필할 수 있으면 최대한 피하는 것이 좋다. Layout System 안에서 자식 UI Element에 <kbd>Dirtying</kbd>이 발생하면 최소 하나의 `GetComponent()`가 호출된다. 이 호출은 UI Element의 부모중 Layout Group을 찾을 때 까지 그리고 Hierarchy Root까지 올라간다.\
따라서 **Nested Layout Group**은 성능에 치명적일 수 있다.

**Dirtying** : Object에 수정이 이루어진다는 뜻.

# 5. UI Pool 올바르게 사용하기

**UI Object**의 부모를 바꿔주고 비활성화 하는 방법은 불필요한 <kbd>Dirtying</kbd>을 발생시킨다. **UI Object**를 먼저 비활성화 한뒤 처리를 하는것이 바람직하다.

# 6. Canvas 숨기는 방법

<kbd>Canvas Component</kbd>를 빌활성화 하는것이 가장 좋은 방법이다. 비활성화 하게되면 Canvas는 **Draw Call**을 멈추게되고 화면에 안그려지게 된다. 하지만 Mesh 와 Vertices 정보는 버리지 않고 간직하고 있다가 <kbd>Canvas Component</kbd>가 다시 활성화되면 재사용하게 된다. 또한, <kbd>Canvas Component</kbd>는 비싼 `OnDisable()`/`OnEnable()`을 호출하지 않는다.

# 7. Animator 사용하기

Animator는 Animation 값에 변화가 없어도 매 프레임 <kbd>Dirtying</kbd>를 발생시킨다. 따라서 꼭 필요한 Dynamic Canvas에서 사용하는 것이 좋다. 