---
layout: post
title: Don't Push The Button
date: 2021-02-23 09:29:20 +0700
modified: 2021-02-23 21:49:47 +07:00
categories: Projects
tags: [HalfLight, Project, VR]
description: VR 타임어택 액션 게임
---

<figure>
<img src="/project-DontPushTheButton/projectpic.png" alt="projectPic">
</figure>

**팀명:** Half-Light\
**팀원:** 박근영, 김현우\
**개발 환경:** Unity 2020.1 URP, Visual Studio, GitLab\
**제작 기간:** 2020.05.01 ~ 2020.08.03 (미완성)\
**YouTube:** [Dungeon Breaker Devlog](https://www.youtube.com/watch?v=nUcayipipwk&t=77s)
<br />

<hr>

# Index

**1. [<kbd> 프로젝트 소개 </kbd>](#프로젝트-소개)**
- [<kbd> 목표 </kbd>](#목표)
- [<kbd> 팀 소개 및 개발 일정 </kbd>](#팀-소개-및-개발-일정)

**2. [<kbd> 프로젝트 구현 </kbd>](#프로젝트-구현)**
- [<kbd> Water System </kbd>](#1-water-system)
- [<kbd> Procedural Terrain </kbd>](#2-procedural-terrain)
- [<kbd> Prototype </kbd>](#3-prototype-result)
- [<kbd> Garbage & Spawner </kbd>](#4-garbage--spawner)
- [<kbd> UI </kbd>](#5-ui)
- [<kbd> Final Result </kbd>](#6-final-result)\
<br />

<hr>

# 프로젝트 소개

<kbd>Don't Push The Button</kbd>은 **VR 전략 액션 게임**이다. 최대한 빠른 시간 안에 맵 어딘가에 위치한 **버튼**을 찾아 눌르면 스테이지를 클리어하게 된다. 난이도가 올라갈 수록 맵에 존재하는 무기의 사용 횟수가 줄어들고 적군 또한 늘어나기 때문에 제한된 자원을 현명하게 사용하는 것이 중요하다.

### 목표
첫 VR 게임을 개발하면서 몰입의 중요성을 알게 되었다. **몰입 경험**은 사용자가 마치 그 가상공간안에 들어가 있는 듯한 느낌을 주는 것으로 크게 Sound, Visual, Interaction 3가지 요소로 나뉠 수 있다고 생각한다. 그 중 **Interaction** 에 많은 시간을 투자하였다. PC 게임과는 다른 VR의 장점을 극대화하고 사용자 경험의 질을 높이는 데 집중했다.

### 팀 소개 및 개발 일정
##### : 팀 소개

<figure>
<img src="/project-DontPushTheButton/teamHalfLight_rec.jpg" alt="Half-Light">
</figure>

| <img class="round" src="profileGY.JPG" width="130px"> | <img class="round" src="profileHW.JPG" width="130px"> |
| :박근영: | :김현우: |
| :---- | :---- |
| - VR Player | - Enemy \
| - Game Systems | - Level Design \
| - Interactive Items | - Interactive Env. \

##### : 개발 일정
@startmermaid
gantt
    dateFormat  YYYY-MM-DD
    section First Month
    주제 선정, 자료 수집    :fm1, 2020-05-03, 6d
    enemies using animation    :fm2, after fm1, 12d
    interactive level     :fm3, after fm2, 7d

    section Second Month
    active ragdoll    :sm1, 2020-06-01, 60d

    section Third Month
    patrol system    :tm1, 2020-07-01, 2d
    navigation system    :tm2, after tm1, 2d

@endmermaid

# 프로젝트 구현

## 1. Enemy

<figure>
<img src="/project-DontPushTheButton/skellieSelfie.png" alt="skellieSelfie.png">
</figure>



### a. Animation Based

<figure>
<img src="/project-DontPushTheButton/animationenemy.png" alt="animationenemy.png">
<figcaption>Fig 3. Animation based Enemy.</figcaption>
</figure>



### b. Ragdoll Based

<figure>
<img src="/project-DontPushTheButton/skellieNight.png" alt="skellieNight.png">
<figcaption>Fig 3. Ragdoll based Enemy.</figcaption>
</figure>
