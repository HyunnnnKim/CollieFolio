---
layout: post
title: Jekyll에 Google Analytics 추가하기
date: 2021-03-12 09:29:20 +0700
modified: 2021-03-12 21:49:47 +07:00
categories: Blog
tags: [TIL, Jekyll]
description: Setting up Google Analytics in Jekyll
---

**Google Analytics**는 적용한 웹사이트의 트레픽을 분석해주는 웹툴이다. **Google Analytics**를 통해 얼마나 많은 사람이 내 웹사이트를 방문하는지, 어느 지역에서 접근하는지 등 다양한 정보를 확인할 수 있다.

## Google Analytics 계정 생성

**Google Analytics**를 적용하기 위해선 [Google Analytics 계정](https://analytics.google.com/analytics/web/provision?authuser=0#provision/SignUp/)이 필요하다.

<figure>
<img src="/blog-Jekyll-GoogleAnalytics/createMID.png" alt="create measurement ID">
<figcaption>Fig 1. Create Measurement ID.</figcaption>
</figure>

계정을 만들었다면 **Data Streams &rarr; Choose Platform** 에서 하나를 선택하고 내 웹사이트를 등록한다.

<figure>
<img src="/blog-Jekyll-GoogleAnalytics/setupStream.png" alt="setup stream">
<figcaption>Fig 2. Add Website.</figcaption>
</figure>

웹사이트를 등록하면 <kbd>Measurement ID</kbd>가 만들어진다.

## Google Analytics, Jekyll에 적용

우선 `analytics.html` 이라는 새로운 파일을 <kbd>_includes</kbd> 폴더 안에 만들어 준다.

```
|-- _includes
| |-- analytics.html
```

`analytics.html` 파일 안에 다음과 같이 채워준다.

``` html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-XXXXXXXXXX');
</script>
```

`'G-XXXXXXXXXX'` 부분을 아까 만든 **Measurement ID** 바꿔주거나 <kbd>&#123;{ site.google_analytics }}</kbd>로 바꿔준다.

후자의 경우 <kbd>_config.yml</kbd> 파일에 `google_analytics: "G-XXXXXXXXXX"` 추가해줘야 한다.

Jekyll이 프로젝트를 빌드할때 `analytics.html`를 모든 페이지에 추가하기 위해
<kbd>{&#37; include analytics.html %}</kbd>를 `default.html`의 헤드에 추가한다.

마지막으로 빌드하면 **Google Analytics**에서 트레픽 정보를 확인할 수 있다.

<figure>
<img src="/blog-Jekyll-GoogleAnalytics/result.png" alt="setup stream">
<figcaption>Fig 3. Result.</figcaption>
</figure>

## Reference

[Google Analytics setup for Jekyll](https://michaelsoolee.com/google-analytics-jekyll/)