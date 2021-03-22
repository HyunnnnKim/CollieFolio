---
layout: post
title: Process of Render Pipeline in Unity
date: 2021-03-12 09:29:20 +0700
modified: 2021-03-12 21:49:47 +07:00
categories: Blog
tags: [TIL, Unity]
description: Process of Render Pipeline in Unity
---

## Render Pipeline 이란?

3D 공간에 있는 오브젝트를 2D 화면에 표현하는 과정을 의미한다.\
이 과정은 크게 3가지 프로세스를 거친다.

Application &rarr; Geometry &rarr; Rasterizer

## Application Stage

오브젝트를 화면에 렌더링 하기 이전의 단계이다. 이 단계는 현재 프레임에서 렌더링이 가능한 오브젝트를 컬링해 최종적으로 렌더링 될 오브젝트를 **선별**한다. 오브젝트가 줄어들 수록 GPU가 처리할 연산량이 줄어들기 때문에 렌더 파이프라인 성능에 직접적인 영향을 끼친다.

<figure>
<img src="/unity-renderpipeline/renderpipeline.png" alt="render pipeline">
<figcaption>Fig 1. Unity Render Pipeline.</figcaption>
</figure>

## Geometry Stage

오브젝트의 지오메트리를 구성하는 버텍스와 폴리곤을 처리하는 단계이다. 버텍스를 화면상에 배치하는 과정을 거친다.

Vertex Transform &rarr; Vertex Shader &rarr; Create Geometry

### Vertex Transform


### Vertex Shader


### Create Geometry


## Rasterizer Stage


