---
layout: post
title: Unity OSX 네이티브 플러그인 만들기
date: 2021-03-11 09:29:20 +0700
modified: 2021-03-11 21:49:47 +07:00
categories: Blog
tags: [TIL, Unity]
description: Creating OSX Native Plugins in Unity using C++
---

유니티는 **Plugin**이라는 형태로 외부에서 개발된 코드를 추가하여 사용할 수 있다. Plugin은 **Managed Plugin** 과 **Native Plugin** 두 가지로 나뉜다.

#### Managed & Unmanaged (Native)

외부에서 컴파일된 코드를 **Dynamic Link Libraries (DLL)** 이라고 한다. DLL은 컴파일된 코드지만 다른 애플리케이션을 통해서만 사용될 수 있다.

**Managed Plugin**은 C#으로 작성되고 바이트코드 언어인 Common Intermediate Language (CIL) 형태로 컴파일돼서 사용된다. 

**Native Plugin**은 다른 언어로 작성된 플랫폼별 라이브러리이다. 기계어로 컴파일돼서 일반 스크립트보다 빠른 경향이 있다.

# 1. OSX 네이티브 플러그인 만들기

XCode를 열어 **macOS &#8594; Framework & Library &#8594; Bundle** 선택해서 프로젝트를 생성한다.

<figure>
<img src="/blog-nativePlugins/createProject.png" alt="create project">
<figcaption>Fig 1. Create Project.</figcaption>
</figure>

C++ 파일을 만들고 간단한 코드를 추가한다.

##### FirstPlugin.hpp

{% highlight cpp %}
#ifndef FirstPlugin_hpp
#define FirstPlugin_hpp

#include <stdio.h>

extern "C" {
const char* SayHello();
int AddTwoIntegers(int, int);
float AddTwoFloats(float, float);
}

#endif /* FirstPlugin_hpp */

{% endhighlight %}



##### FirstPlugin.cpp

{% highlight cpp %}
#include "FirstPlugin.hpp"

const char* SayHello(){
    return "Hello!";
}

int AddTwoIntegers(int a, int b){
    return a + b;
}

float AddTwoFloats(float a, float b){
    return a + b;
}
{% endhighlight %}

빌드하면 다음과 같이 `.bundle` 파일이 생긴다. 이제 번들 파일을 유니티로 가져가 <kbd>Plugins</kbd> 폴더에 넣어서 사용하면 된다.

<figure>
<img src="/blog-nativePlugins/build.png" alt="build file">
<figcaption>Fig 2. Build File.</figcaption>
</figure>

# 2. 유니티에서 사용하기

간단하게 UI 텍스트를 플러그인의 `SayHello()` 함수를 이용해 바꿔주는 코드다. 

##### PluginPrinter.cs

{% highlight cs %}
using System;
using System.Runtime.InteropServices;
using TMPro;
using UnityEngine;

public class PluginPrinter : MonoBehaviour
{
    [DllImport("TestPlugin")]
    private static extern IntPtr SayHello();

    [DllImport("TestPlugin")]
    private static extern int AddTwoIntegers(int a, int b);

    [DllImport("TestPlugin")]
    private static extern float AddTwoFloats(float a, float b);

    [SerializeField]
    private TextMeshProUGUI output = null;

    private void Start()
    {
        output.text = Marshal.PtrToStringAnsi(SayHello());
    }
}
{% endhighlight %}

엔트리 포인트 정의는 `DllImport("BundleName")`를 통해서 한다.

<figure>
<img src="/blog-nativePlugins/hello.png" alt="result">
<figcaption>Fig 3. Result.</figcaption>
</figure>