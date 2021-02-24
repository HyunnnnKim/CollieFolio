---
layout: post
title: BeatBreaker
date: 2021-02-19 09:29:20 +0700
modified: 2021-02-19 21:49:47 +07:00
categories: Projects
tags: [RAPA, Project, VR]
description: 날아오는 비트를 비트에 맞게 치는 VR 리듬 복싱 게임.
---

<figure>
<img src="/project-BeatBreaker/beatlogo.png" alt="BeatBreaker Logo">
</figure>

**팀명:** 드랍더빗\
**팀원:** 강승기, 김현우\
**개발 환경:** Unity 2020.1 URP, Visual Studio, GitLab\
**제작 기간:** 2020.10.27 ~ 2020.11.24\
<br />

<hr>

# Index

**1. [<kbd> 프로젝트 소개 </kbd>](#프로젝트-소개)**
- [<kbd> 구조 </kbd>](#구조)
- [<kbd> 역할 분담 및 개발 일정 </kbd>](#역할-분담-및-개발-일정)

**2. [<kbd> 프로젝트 구현 </kbd>](#프로젝트-구현)**
- [<kbd> VR PLAYER </kbd>](#1-vr-player)
- [<kbd> MAIN ROOM </kbd>](#2-main-room)
- [<kbd> EXERCISE STAGE </kbd>](#3-exercise-stage)\
<br />

<hr>

# 프로젝트 소개

<kbd>Beat Breaker</kbd>는 언택트 시대에 사는 사람들을 위한 홈트레이닝을 목적으로 만들어진 **VR 피트니스 어플리케이션**이다.

대부분의 사람은 홈트레이닝을 지루하게 생각하고 운동을 오래 끌고 가지 못한다. 이러한 문제점을 보완하기 위해 **리듬 게임** 요소를 추가하였다.
##### : target game

| <img src="/project-BeatBreaker/targetBS.png" alt="targetBS.png"> | <img src="/project-BeatBreaker/targetVRB.png" alt="targetVRB.png"> |

<figure><figcaption>Fig 1. Target Games.</figcaption></figure>

타겟은 **Beat Saber**와 **VR Box**로 잡았다. **Beat Saber**의 네온 분위기와 **VR Box**의 타격감을 최대한 살리려고 했다.

### 구조
<kbd>Beat Breaker</kbd>는 크게 3가지 파트로 나뉜다; <kbd>Main Room</kbd>, <kbd>Exercise Stage</kbd>, <kbd>Result</kbd>. 

##### : main state diagrams

@startmermaid
stateDiagram-v2
    [*] --> Active
    state Active {
        [*] --> MainRoom
        MainRoom --> [*] : quit
        MainRoom --> Exercise : selectMusic
        Exercise --> Result : timeEnd
        Result --> MainRoom : addRank 
    }
    Active --> [*]
@endmermaid

<kbd>Main Room</kbd> 에서는 앨범과 노래를 선택할 수 있다. 노래를 선택하면 <kbd>Exercise</kbd> 파트에서 열심히 비트를 쳐서 점수를 올리고 <kbd>Result</kbd> 에서 결과를 확인하고 이름과 기록을 남길 수 있다.

### 역할 분담 및 개발 일정
##### : 팀 구성원

| :강승기: | :김현우: |
| :----- | :----- |
| - 비트 스폰     | - VR 플레이어        \
| - 비트 리듬 생성 | - Game System      \
| - 장애물 움직임  | - Interactive UI   \ 
|               | - Level Design     \ 

##### : 개발 일정
@startmermaid
gantt
    dateFormat  YYYY-MM-DD
    section 프로토타입
    타겟 선정    :proto1, 2020-10-27, 1d
    VR 플레이어    :proto2, after proto1, 3d
    VR Physics Button    :proto3, after proto2, 3d
    Music Selection    :proto4, after proto3, 4d
    VR Boxing Bag    :proto5, after proto4, 2d
    Bug fix    :proto6, after proto5, 3d
    section 알파
    Game System    :alpha1, 2020-11-10, 4d
    Score Manager    :alpha2, 2020-11-13, 2d
    Scene Manager    :alpha3, 2020-11-14, 3d
    section 베타
    Level Design    :beta1, 2020-11-17, 3d
    Sound & Effect    :beta2, after beta1, 2d
@endmermaid

# 프로젝트 구현

## 1. VR PLAYER

<figure>
<img src="/project-BeatBreaker/vrplayer.png" alt="vrplayer.png">
</figure>

#### : interaction level
**VR**에서 플레이어는 중심축 역할을 한다. 플레이어와 주변 환경, 사물 간의 **인터렉션 레벨**에 따라 사용자의 경험도 달라질 것이다. <kbd>Beat Breaker</kbd>의 플레이어는 핸드 인터렉션이 대부분을 차지하기 때문에 글러브의 인터렉션 레벨을 높일 필요가 있었다. 글러브 인터렉션 레벨은 다음과 같다.
- 글러브는 항상 컨트롤러를 따라 다녀야 한다.
- 글러브는 모든 오브젝트(정적, 동적)와 충돌이 가능해야 된다. (글러브가 통과하는 일이 없어야 된다)
- 비트나 샌드백을 치면 충돌이 난 지점에서 소리가 나야 된다.

### a. XR Input
#### : input class diagram

@startmermaid
classDiagram
    ScriptableObject <|-- XRInputHandler
    XRInputHandler <|-- XRButtonHandler
    XRInputHandler <|-- XRAxisHandler
    XRInputHandler <|-- XRAxis2DHandler
    class XRInputHandler {
        +HandleState(XRController)
    }
    class XRButtonHandler {
        +bool Value
        +HandleState(XRController)
    }
    class XRAxisHandler {
        +float Value
        +HandleState(XRController)
    }
    class XRAxis2DHandler {
        +Vector2 Value
        +HandleState(XRController)
    }
    XRInputManager --|> Monobehaviour
    XRInputManager --> XRButtonHandler
    XRInputManager --> XRAxisHandler
    XRInputManager --> XRAxis2DHandler
    class XRInputManager {
        +List~XRButtonHandler~ buttons
        +List~XRAxisHandler~ axes
        +List~XRAxis2DHandler~ 2dAxes
        +Update()
    }
@endmermaid

**Unity XR Toolkit**은 최근에서야 **New Input System**으로 인풋 스크립트가 딸려 오지만 그 전까지는 인풋 매니저를 따로 작성해야 했다. 프로젝트에서 사용한 인풋 매니져는 [VR with Andrew](https://www.youtube.com/channel/UCG8bDPqp3jykCGbx-CiL7VQ)의 **Scriptable Object** 방식을 사용했다.

인풋은 3가지로 나뉜다. <kbd>XRButtonHandler</kbd>는 Boolean 값을 반환하는 **Primary/Secondary Button**을 받고 <kbd>XRAxisHandler</kbd>는 하나의 축으로만 값이 변하는 **Trigger Button**, **Grip Button** 이 여기에 해당한다.
<kbd>XRAxis2DHandler</kbd>는 두개의 축을 가진 **Primary/Secondary Joystick**이 되겠다.

<kbd>XRInputManager</kbd>는 위에 3가지 Handler로 만들어진 **Scriptable Object**를 받아 Update에서 `HandleState()`를 통해 값을 계속 업데이트 해준다.

인풋 값이 필요한 스크립트는 해당 인풋의 **Scriptable Object**를 받아서 사용하면 된다.

### b. Physics Hand

<figure>
<img src="/project-BeatBreaker/physicshand.png" alt="physicshand.png">
<figcaption>Fig 2. Physics Hand.</figcaption>
</figure>

플레이어의 글러브는 **정적**인 사물과도 충돌이 있어야 하기 때문에 **Non-Kinematic Rigidbody** 를 가진다. 이렇게 되면 컨트롤러의 포지션과 로테이션 트레킹을 해야 되는데 이 문제는 [VR with Andrew](https://www.youtube.com/channel/UCG8bDPqp3jykCGbx-CiL7VQ)의 팁을 받아 <kbd>Velocity Tracking</kbd> 방법을 사용하였다.

##### XRPhysicsHand.cs

{% highlight cs %}
private void VelocityTrackPosition()
{
    _rb.velocity *= velocityPredictionFactor;
    var positoinDelta = targetPosition - _rb.worldCenterOfMass;
    var velocity = positoinDelta / Time.unscaledDeltaTime;

    if (!float.IsNaN(velocity.x))
    {
        _rb.velocity += velocity;
    }
}
{% endhighlight %}

{% highlight cs %}
private void VelocityTrackRotation()
{
    _rb.angularVelocity *= velocityPredictionFactor;
    var rotationDelta = targetRotation
        * Quaternion.Inverse(_rb.rotation);
    float angleInDegrees; Vector3 rotationAxis;
    rotationDelta.ToAngleAxis(out angleInDegrees, out rotationAxis);
    if (angleInDegrees > 180)
    {
        angleInDegrees -= 360;
    }

    if (Mathf.Abs(angleInDegrees) > Mathf.Epsilon)
    {
        var angularVelocity = (rotationAxis * angleInDegrees
            * Mathf.Deg2Rad) / Time.unscaledDeltaTime;
        if (!float.IsNaN(angularVelocity.x))
        {
            _rb.angularVelocity += angularVelocity
                * angularVelocityDamping;
        }
    }
}
{% endhighlight %}

<kbd>Velocity Tracking</kbd> 방식은 **Unity XR Toolkit**에서 <kbd>GrabbleObject</kbd>를 잡을 때 사용하는 방식중 하나로 글러브에도 적용하게 되면 컨트롤러의 포지션과 로테이션값을 따라가게 된다.

### c. Persistent Scene

빌드 인덱스의 0번째에 씬, <kbd>Persistent Scene</kbd>은 파괴되면 안 되는 오브잭트들이 존재하는 씬으로 GameSystem, ScoreManager 및 VRPlayer가 포함된다.

<kbd>Persistent Scene</kbd>은 항상 로드 되어 있고 그 위에 새로운 씬이 로드되는 방식이다.

##### SceneLoader.cs

{% highlight cs %}
private IEnumerator LoadNew(string sceneName)
{
    AsyncOperation loadAsyncOperation =
        SceneManager.LoadSceneAsync(sceneName, LoadSceneMode.Additive);

    while (!loadAsyncOperation.isDone)
    {
        float progress = Mathf.Clamp01(
            loadAsyncOperation.progress / .9f);
        yield return null;
    }
}
{% endhighlight %}

새로운 씬을 로드할 때 `LoadSceneMode.Additive` 모드로 로드하면 <kbd>Persistent Scene</kbd>이 유지된 채로 새로운 씬이 로드된다.

{% highlight cs %}
private IEnumerator LoadScene(string sceneName)
{
    _isLoading = true;

    OnLoadBegin?.Invoke();
    yield return _screenFader.StartFadeIn();
    yield return StartCoroutine(UnloadCurrent());

    yield return new WaitForSeconds(1f);

    yield return StartCoroutine(LoadNew(sceneName));
    yield return _screenFader.StartFadeOut();
    OnLoadEnd?.Invoke();

    _isLoading = false;
}
{% endhighlight %}

새로운 씬을 로드하는 과정은 위와 같다. 엑티브된 씬을 언로드 하기전에 화면을 **FadeIn** 했다가 새로운 씬을 로드하면서 **FadeOut**을 한다.

##### ScreenFader.cs

{% highlight cs %}
private IEnumerator FadeIn()
{
    while (_smh.shadows.value.magnitude >= 1)
    {
        _smh.shadows.value -= Vector4.one * _fadeSpeed * Time.deltaTime;
        _smh.midtones.value -= Vector4.one * _fadeSpeed * Time.deltaTime;
        _smh.highlights.value -= Vector4.one * _fadeSpeed * Time.deltaTime;
        yield return null;
    }
}
{% endhighlight %}

<kbd>ScreenFader</kbd>는 PostProcess의 `ShadowsMidtonesHighlights`을 이용한다.

## 2. MAIN ROOM

<figure>
<img src="/project-BeatBreaker/mainroom.gif" alt="mainroom.gif">
</figure>

<kbd>MainRoom</kbd>은 앨범을 고르고 샌드백을 쳐서 노래를 고르는 공간이다. 

### a. BoxingBag

<figure>
<img src="/project-BeatBreaker/beatMain.png" alt="beatMain.png">
<figcaption>Fig 3. Boxing Bag.</figcaption>
</figure>

<kbd>BoxingBag</kbd>은 <kbd>MainRoom</kbd>의 **컨트롤러** 역할을 한다. Prototype 버전에는 버튼을 이용해 노래를 고르는 방식이었지만 재미가 없는 인터렉션이라 <kbd>BeatBreaker</kbd>의 컨셉과도 맞는 샌드백으로 바꿨다.

조작 방법은 간단하다. <kbd>BoxingBag</kbd>의 **오른쪽**을 치면 노래가 오른쪽으로 넘어가고 **왼쪽**을 치면 왼쪽으로 넘어간다. 그리고 **가운데**를 강하게 치면 노래가 선택된다. 

#### : structure

| <img src="/project-BeatBreaker/chain.png" alt="chain.png"> | <img src="/project-BeatBreaker/bagspring.png" alt="bagspring.png"> |

<figure><figcaption>Fig 4. Boxing Bag Structure.</figcaption></figure>

<kbd>BoxingBag</kbd>은 **Rigidbody**로 이루어져 있다. 체인은 **Configurable Joint**로 연결돼 있고 고정축만 **Kinematic**이다. <kbd>BoxingBag</kbd> 에 무게감을 주기 위해 약간에 *drag*값이 있다. 또, 쳤을 때 너무 멀리 날아가면 안 되고 타격감을 주기 위해 바닥에는 **Spring Joint**가 <kbd>BoxingBag</kbd>에 연결되어 있다.

#### : flicker

<figure>
<img src="/project-BeatBreaker/bagBlink.gif" alt="bagBlink.gif">
<figcaption>Fig 5. Flicker.</figcaption>
</figure>

<kbd>BoxingBag</kbd>은 글러브로 치면 컨셉에 맞게 네온 라이트가 깜빡인다. 

##### BoxingBag.cs

{% highlight cs %}
private IEnumerator Flick(float duration)
{
    float elapsedTime = 0f;
    while (elapsedTime <= duration)
    {
        RandomAverage(_renderer, _color);
        elapsedTime += Time.deltaTime;
        yield return null;
    }
}

private Color RandomAverage(Renderer renderer, Color color)
{
    while (_smoothQueue.Count >= _smoothing)
    {
        _smoothSum -= _smoothQueue.Dequeue();
    }
    float randomValue = Random.Range(_minValue, _maxValue);
    _smoothQueue.Enqueue(randomValue);
    _smoothSum += randomValue;
    renderer.material.SetColor("_EmissionColor", color * _smoothSum);
    return color;
}
{% endhighlight %}

 원리는 결국 랜덤 수를 이용해 깜빡이게 하는 것이다. 큐가 정해진 `_smoothing`의 크기만큼 다 찰 때까지 *intensity*가 올라갔다가 정해진 수와 같아지면 큐에서 하나씩 빼서 *intensity*의 값에 변동을 준다.

### b. LevelSelector

<figure>
<img src="/project-BeatBreaker/levelselector.png" alt="levelselector.png">
<figcaption>Fig 6. Level Selector.</figcaption>
</figure>

<kbd>LevelSelector</kbd>는 <kbd>BoxingBag</kbd>의 **Visual Output** 이다. UI 이지만 3D 오브젝트를 이용해 만들었다. <kbd>LevelSelector</kbd>는 앨범을 소켓에 넣는 것부터 시작된다.

#### : select album

<figure>
<img src="/project-BeatBreaker/album.gif" alt="album.gif">
<figcaption>Fig 7. Album.</figcaption>
</figure>

소켓은 Unity XR Toolkit의 Socket을 이용했다. 

##### LevelSelector.cs

{% highlight cs %}
/// <summary>
/// This is called when music block is attached to the socket.
/// </summary>
public void OnMusicBlockAttach(GameObject socket)
{
    _isMusicBoxAttached = true;

    GameObject interactor = socket.GetComponent<XRSocketInteractor>().selectTarget.gameObject;
    _selectedMusicBlock = interactor.GetComponent<MusicBlock>();
    _musicBlockAttach.PlaySoundAt(interactor.transform.position, 0.3f, 2f);

    _firstSongIndex = _selectedMusicBlock.FirstSongIndex;
    _lastSongIndex = _selectedMusicBlock.LastSongIndex;
    _selectedSceneIndex = _firstSongIndex;
    _selectedSceneTitle = SceneLoader.Instance.Scenes[_selectedSceneIndex];

    AttachVisualize();
}
{% endhighlight %}

소켓에 앨범이 부착되는 순간 호출되고 `XRSocketInteractor`로 부터 앨범에 있는 **노래 정보**를 가져온다. 노래 정보를 가져오면 **노래 커버**가 보이고 노래도 재생된다. 

#### : select music

<figure>
<img src="/project-BeatBreaker/selectMusic.gif" alt="selectMusic.gif">
<figcaption>Fig 8. Select Music.</figcaption>
</figure>

UI의 디테일을 살리기 위해 노래 커버가 옆으로 넘어갈 때 마다 Lerp를 통한 **자리 이동**이 되고 **스케일**과 **투명도**도 바뀐다.

##### LevelSelector.cs

{% highlight cs %}
private IEnumerator SmoothFade(GameObject songCover, Vector3 startScale, Vector3 targetScale, Color startColor, float startAlpha, float targetAlpha,
    AnimationCurve ac, float duration, FadeMode fadeMode)
{
    if (fadeMode.Equals(FadeMode.In)) songCover.SetActive(true);
    float elapsedTime = 0f;

    while (elapsedTime <= duration)
    {
        // 노래 커버의 투명도 조정
        Material material = songCover.GetComponent<Renderer>().material;
        startColor.a = Mathf.Lerp(startAlpha, targetAlpha, ac.Evaluate(elapsedTime / duration));
        material.color = startColor;

        // 사진의 투명도 조정
        Color imageColor = songCover.GetComponentInChildren<Image>().color;
        imageColor.a = Mathf.Lerp(startAlpha, targetAlpha, ac.Evaluate(elapsedTime / duration));
        songCover.GetComponentInChildren<Image>().color = imageColor;

        // 스케일 조정
        _lerpScaleValue = Vector3.Lerp(startScale, targetScale, ac.Evaluate(elapsedTime / duration));
        songCover.transform.localScale = _lerpScaleValue;

        elapsedTime += Time.deltaTime;
        yield return null;
    }
    if (fadeMode.Equals(FadeMode.Out)) songCover.SetActive(false);
}
{% endhighlight %}
투명도를 조정하기 위해 material의 **Render Mode**는 **Transparent**를 사용하고 사진 material의 우선순위를 더 높게 줬다.
자리 이동 모션도 `SmoothFade()`와 마찬가지 방법을 사용한다.

#### : confirm selection

<figure>
<img src="/project-BeatBreaker/musicPick.gif" alt="musicPick.gif">
<figcaption>Fig 9. Confirm Selected Music.</figcaption>
</figure>

노래 선택은 <kbd>BoxingBag</kbd>에 중앙을 일정 속도 이상으로 쳐야 선택이 된다.

##### SandBagHitPoint.cs

{% highlight cs %}
private void TiggerEvent(Rigidbody hand)
{
    BoxingBag.ActivateFlicker();

    if (_selectedHitFunc.Equals(HitFunctions.Play))
    {
        _eventTriggerValue = BoxingBag.PlayTriggerValue;
        // Play
        if (hand.velocity.magnitude > _eventTriggerValue)
        {
            BoxingBag.DisconnectBoxingBag(hand);
            onHitSandBag?.Invoke(_selectedHitFunc);
        }
    }
    else
    {
        _eventTriggerValue = BoxingBag.HitTriggerValue;
        // Previous, Next
        if (hand.velocity.magnitude < 6f &&
            hand.velocity.magnitude > _eventTriggerValue)
        {
            onHitSandBag?.Invoke(_selectedHitFunc);
        }
    }
}
public enum HitFunctions { Previous, Play, Next }
{% endhighlight %}

<kbd>BoxingBag</kbd>의 **OnTriggerEnter** 콜백에 의해 호출된다. 미리 설정된 `_selectedHitFunc`에 따라 <kbd>LevelSelector</kbd>에 전달되는 값이 달라진다.

##### BoxingBag.cs

{% highlight cs %}
public void DisconnectBoxingBag(Rigidbody rb)
{
    _rootChain.breakForce = 1f;
    _springChain.breakForce = 1f;
    ForceTarget.drag = 0f;
    ForceTarget.AddForceAtPosition(rb.velocity * _breakForce,
        rb.position, ForceMode.Impulse);
    ChainBreakSound.PlaySoundAt(_rootChain.transform.position);
}
{% endhighlight %}

체인은 joint의 `.breakForce` 값을 1로 바꿔서 끊고 충돌 지점에 `.AddForceAtPosition()`를 하였다. 

#### : Game State Changer

<kbd>Game State</kbd>은 총 4가지이다.

{% highlight cs %}
public enum GameState { Load, MapSelection, GameOn, Result }
{% endhighlight %}

<kbd>GameStateChanger</kbd>가 현재 상태를 결정하고 상태마다 필요한 세팅을 실행한다.

##### GameStateChanger.cs

{% highlight cs %}
private void OnEnable()
{
    SceneManager.sceneLoaded += UpdateGameState;
}
{% endhighlight %}

다음 3가지 상태; <kbd>Load</kbd>, <kbd>MapSelection</kbd>, <kbd>GameOn</kbd> 는 새로운 씬이 로드 될 때 `UpdateGameState()` 함수에서 업데이트된다. 마지막 <kbd>Result</kbd> 상태는 <kbd>GameOn</kbd> 상태에서 선택한 노래가 끝나면 자동으로 바뀐다.

## 3. EXERCISE STAGE

<figure>
<img src="/project-BeatBreaker/gameon.gif" alt="gameon.gif">
</figure>

<kbd>Exercise Stage</kbd>는 비트에 맞춰 날아오는 <kbd>Beat</kbd>를 방향에 맞게 치면서 운동하는 공간이다.

### a. Beat

<figure>
<img src="/project-BeatBreaker/beat.png" alt="beat.png">
<figcaption>Fig 10. Beat.</figcaption>
</figure>

<kbd>Beat</kbd>는 스폰이될 때 치는 방향이 랜덤으로 정해지고 레인을 따라 플레이어를 향해 움직인다. <kbd>Beat</kbd>는 기본적으로 물리 인터렉션이 가능하다.

플레이어는 점수를 올리기 위해서 정해진 방향에 맞게 <kbd>Beat</kbd>를 쳐야 되고 콤보를 유지하는 것이 중요하다.

#### : points

| <img src="/project-BeatBreaker/beatHit.gif" alt="beatHit.gif"> | <img src="/project-BeatBreaker/beatMiss.gif" alt="beatMiss.gif"> |

<figure><figcaption>Fig 11. Hit & Miss.</figcaption></figure>

<kbd>Beat</kbd>를 쳤을 때 점수를 측정하는 절차는 다음과 같다.

**OnCollisionEnter** &#8594; **Check Speed** &#8594; **Check HitPoint** &#8594; **Calculate Angle**

##### BoxingBag.cs

{% highlight cs %}
// 가장 먼져 속도 체크로 Hit, Miss 인지 판별한다.
if (_rb.velocity.magnitude > noteControl.TriggerVelocity)
{
    EffectPool.Instance.PlayEffect(EffectType.punch, collision.contacts[0].point, -_rb.transform.forward);
    _goodPunch.PlaySoundAt(collisionPoint);
    _shadowPunch.PlaySoundAt(collisionPoint);

    // 충돌 지점이 HitPoint 범위에 있는지 확인 한다. 
    if (dstFromHitPoint < noteControl.HitPointRadius)
    {
        // 벡터의 내적을 구해 마지막 파이널 점수에 적용한다.
        float dotValue = Vector3.Dot(-hitPoint.up, _rb.velocity.normalized);
        _finalScore += score + Mathf.Clamp(score * dotValue, 0f, score);
        ScoreManager.Instance.AddScore(_finalScore);
        noteRB.AddForceAtPosition(punchVelocity, collisionPoint, ForceMode.Impulse);
    }
    else
    {
        _finalScore = score * Mathf.InverseLerp(0, 1, dstFromHitPoint);
        ScoreManager.Instance.AddScore(_finalScore);
        noteRB.AddForceAtPosition(punchVelocity * _reducePercentage, collisionPoint, ForceMode.Impulse);
    }
}
else
{
    _badPunch.PlaySoundAt(collisionPoint);
    ScoreManager.Instance.Missed();
}
{% endhighlight %}



### b. Fever Mode

<kbd>Fever Mode</kbd>는 10 콤보 이상 유지하면 진입하게 된다.

<figure>
<img src="/project-BeatBreaker/feverEnter.gif" alt="feverEnter.gif">
<figcaption>Fig 12. Enter Fever.</figcaption>
</figure>

하지만 장애물에 충돌하거나 <kbd>Beat</kbd>를 놓치거나 Miss가 날 경우 콤보는 깨지고 <kbd>Fever Mode</kbd>는 끝나게 된다.

<figure>
<img src="/project-BeatBreaker/feverEnd.gif" alt="feverEnd.gif">
<figcaption>Fig 13. Exit Fever.</figcaption>
</figure>

| <img src="/project-BeatBreaker/gameonlight.png" alt="gameonlight.png"> | <img src="/project-BeatBreaker/gameonfever.png" alt="gameonfever.png"> |

<figure><figcaption>Fig 14. Comparison.</figcaption></figure>

### c. Result Screen

<figure>
<img src="/project-BeatBreaker/result.png" alt="result.png">
<figcaption>Fig 15. Result UI.</figcaption>
</figure>

<kbd>Result UI</kbd>는 3개의 페널로 이루어져 있다. **왼쪽 페널**은 *Best Combo*나 *Miss*의 개수 같은 디테일을 표시한다. **가운데 페널**은 점수와 노래 커버사진을 표시하고 **오른쪽 페널**은 플레이한 노래의 랭크가 표시된다.

기록을 남기기 위해선 키보드를 글러브로 치고 <kbd>Enter</kbd>를 누르면 랭크 패널에 기록을 남기게 된다.

#### : physics button
<figure>
<img src="/project-BeatBreaker/keyboard.gif" alt="keyboard.gif">
<figcaption>Fig 16. Physics Keyboard.</figcaption>
</figure>

<kbd>Physics Button</kbd>은 목적에 맞게 재정의 해서 사용할 수 있도록 만들었다.

@startmermaid
classDiagram
    Monobehaviour <|-- PhysicsButton
    PhysicsButton <|-- KeyboardButtons
    KeyboardButtons .. KeyboardController
    class PhysicsButton {
        -ButtonSettings()
        -IsButtonPressed()
        +OnButtonDown()
        +OnButtonUp()
    }
    class KeyboardButtons {
        +event KeyboadKeyFunction
        +KeyboardKeys _selectedKey
        +OnButtonDown(Vector3)
    }
    class KeyboardController {
        -IgnoreCollisions()
        -Push(KeyboardKeys, Vector3)
        -KeyInput(string)
        -Menu()
        -Delete()
        -Enter()
    }
@endmermaid

##### PhysicsButton.cs

{% highlight cs %}
private void IsButtonPushed()
{
    _currentDistance = Vector3.Distance(_btn.transform.position,
        _pushPoint);
    if (_currentDistance < _distance * 0.5f && !_isButtonPushing)
    {
        _isButtonPushing = true;
        OnButtonDown(_btn.transform.position);
    }
    else if (_currentDistance > _distance * 0.6f && _isButtonPushing)
    {
        _isButtonPushing = false;
        OnButtonUp(_btn.transform.position);
    }
}
{% endhighlight %}

<kbd>PhysicsButton</kbd>은 코어 기능으로 버튼이 눌렸는지를 판단한다. `._pushPoint`와 `._btn.transform.position` 사이의 **거리**로 눌렸는지 확인한다.

##### PhysicsButton.cs

{% highlight cs %}
public virtual void OnButtonDown(Vector3 buttonPosition) { }
public virtual void OnButtonUp(Vector3 buttonPosition) { }
{% endhighlight %}

<kbd>PhysicsButton</kbd>을 상속받은 자식은 목적에 맞게 재정의 해서 사용하면 된다.

##### KeyboardButtons.cs

{% highlight cs %}
public override void OnButtonDown(Vector3 position)
{
    onKeyPush?.Invoke(_selectedKey, position);
}
{% endhighlight %}

재정의된 함수는 버튼이 눌릴 때마다 호출된다.

##### KeyboardKeyController.cs

{% highlight cs %}
private void OnEnable()
{
    foreach (KeyboardButtons btn in _keyboardButtons)
    {
        btn.onKeyPush += Push;
    }
}

private void Push(KeyboardKeys key, Vector3 pos)
{
    _click.PlaySoundAt(pos);
    switch (key)
    {
        default:
            KeyInput(key.ToString());
            break;
    }
}
{% endhighlight %}

<kbd>KeyboardKeyController</kbd>에 `Push()` 함수는 `.onKeyPush`에 구독돼 있기 때문에 레퍼런스를 가지고 있는 버튼들이 눌릴 때마다 `KeyInput()`의 기능을 수행한다.

#### : save system

랭크는 기록될 때 **json** 파일 형식으로 저장된다. Tuple에 데이터를 받을 때 노래 제목, 랭크에 기록할 이름, 그리고 플레이한 노래의 점수를 받게 된다.

##### SaveSystem.cs

{% highlight cs %}
public void Save(string song, string name, float score)
{
    Records.Add(new Tuple<string, string, float>(song, name, score));
    var rank = (from record in Records
                orderby record.Item3 descending
                select record)
                .ToList<Tuple<string, string, float>>();

    string jsonRecord = JsonConvert.SerializeObject(rank);
    byte[] bytes = System.Text.Encoding.UTF8.GetBytes(jsonRecord);
    string format = Convert.ToBase64String(bytes);

    File.WriteAllText(SAVE_FOLDER + "Ranking.json", format);
}
{% endhighlight %}

기록은 추가될 때마다 점수순으로 다시 정렬해서 기록된다.

{% highlight cs %}
public List<Tuple<string, string, float>> GetRankOfSong(string song)
{
    List<Tuple<string, string, float>> rank = new List<Tuple<string, string, float>>();
    foreach (Tuple<string, string, float> record in Records)
    {
        if (record.Item1.Equals(song))
        {
            rank.Add(record);
            if (rank.Count == _rankTop) break;
        }
    }
    return rank;
}
{% endhighlight %}

노래의 기록을 가져올 때는 `Records`에 로드된 정보에 미리 설정한 개수만큼만 반환한다.