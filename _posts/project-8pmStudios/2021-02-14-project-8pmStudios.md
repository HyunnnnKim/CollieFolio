---
layout: post
title: 8pm STUDIOS
date: 2021-01-14 09:29:20 +0700
modified: 2021-02-17 21:49:47 +07:00
categories: Projects
tags: [RAPA, Project, VR]
description: 8pm STUDIOS는 아마추어 배우, 작가, 연출, 촬영감독들을 연결하는 가상 영화 촬영 플랫폼이다.
image: /project-8pmStudios/intro.png
---

<figure>
<img src="/project-8pmStudios/intro.png" alt="8pm Studios intro">
</figure>

**팀명:** 8pm\
**팀원:** 김지윤, 신철호, 김현우\
**개발 환경:** Unity 2020.1 URP, Visual Studio, GitLab\
**제작 기간:** 2020.12.28 ~ 2021.02.08\
<br />

<hr>

# Index

**1. [<kbd> 프로젝트 소개 </kbd>](#프로젝트-소개)**
- [<kbd> 목적 </kbd>](#목적)
- [<kbd> 구조 </kbd>](#구조)
- [<kbd> 역할 분담 및 수행 절차 </kbd>](#역할-분담-및-수행-절차)

**2. [<kbd> 프로젝트 기능 </kbd>](#프로젝트-기능)**
- [<kbd> FilmCamera </kbd>](#1-filmcamera)
- [<kbd> ControlDeskSystem </kbd>](#2-controldesksystem)
- [<kbd> CameraPathCreator </kbd>](#3-camerapathcreator)\
<br />

<hr>

# 프로젝트 소개

<kbd>8pm STUDIOS</kbd>는 배우, 작가, 연출, 촬영감독을 위한 **가상 영화 촬영 플랫폼**이다.

### 목적

경제적, 물리적 제약으로 인해 자신의 작품을 만들기 어려운 영화인과 영화인 지망생들이 VR 상에서 자유롭게 배경 및 소품을 사용하고 서로 네트워킹하여 작품을 촬영할 수 있다.

**니즈**
- 다양한 작품의 환경 안에서 연기하고 싶은 배우
- 오리지널 컨텐츠를 선보이고 싶은 감독 및 작가
- 프리 비주얼(Pre-Visualization)을 통한 동영상 콘티 제작
- 컨텐츠를 발굴해야 하는 제작자 등

**기대 효과**
- 영화 제작 진입 장벽을 낮춤
- 영화 및 애니메이션 제작을 위한 가상현실 콘티 등으로 활용을 도모
- 2차 창작을 통해 영화팬들에게 영화 속 주인공이 된 듯한 체험을 제공

### 구조

사용자들은 제작 영화의 주제 또는 키워드별로 방을 만들고 필요한 역할을 구할 수 있다. 팀이 구성되면 배우는 가상 환경 속에서 연기를 하고 연출감독은 환경 세팅을 하며 촬영감독은 촬영을 진행한다. 전체적인 플로우는 다음과 같다.

### 역할 분담 및 수행 절차
##### 팀 구성원

| <img class="round" src="profileHW.JPG" width="130px"> | <img class="round" src="profileJY.jpeg" width="130px"> | <img class="round" src="profileCH.jpeg" width="130px"> |
| 김현우               | 김지윤                 | 신철호                 |
| :----------------: | :-------------------: | :------------------: |
| 카메라 기능, 컨트롤 데스크   | 콘텐츠 기획, 인벤토리, 로비 | 네트워크, 플레이어 인터렉션 |

##### 개발 일정 : 카메라
@startmermaid
gantt
    dateFormat  YYYY-M-D
    section 사전 기획
    프로젝트 기획 및 주제 선정    :pre1, 2020-12-28, 3d
    기획안 작성, 레퍼런스 수집    :pre2, 2020-12-31, 4d
    section 프로토타입
    카메라 녹화, 줌, 포커스    :proto1, 2021-01-04, 4d
    카메라 클래퍼보드, 프리뷰 화면    :proto2, 2021-01-08, 2d
    VR input 연결    :proto3, after proto2, 1d
    section 알파
    버그 수정    :alpha1, 2021-01-12, 3d
    Refactoring, 이벤트로 변경    :alpha2, 2021-01-13, 3d
    카메라 컨트롤러, 선택 녹화    :alpha3, after alpha2, 1d
    카메라 컨트롤러, 프리뷰 전환    :alpha4, after alpha3, 3d
    카메라 컨트롤러, 녹화된 영상 재생    :alpha5, after alpha4, 4d
    카메라 컨트롤러, 뷰 모드/ 녹화 모드    :alpha6, after alpha5, 1d
    section 베타
    카메라 경로, 베이지어 커브    :beta1, 2021-01-26, 3d
    카메라 경로, 생성기    :beta2, after beta1, 3d
    카메라 경로, 버그 수정    :beta3, after beta2, 4d
    영상 촬영, 발표준비    :beta4, after beta3, 2d
@endmermaid

# 프로젝트 기능

## 1. FilmCamera

<figure>
<img src="/project-8pmStudios/cameraFunctions.png" alt="Camera Functions">
<figcaption>Fig 1. Camera Functions.</figcaption>
</figure>

가상 영화 촬영 시 촬영감독이 사용할 카메라이다. 물리적인 컨트롤러를 사용하지 않고 VR 버튼과 조이스틱 인터렉션을 통해 카메라를 제어할 수 있다. 이를 통해 사용자의 **몰입감**과 **사용자 경험**의 퀄리티도 높일 수 있다. 또한 카메라의 기능을 사용자의 니즈에 맞게 설계할 수 있다. 너무 많은 버튼을 한 번에 제공 하는 것 보다 원하는 기능을 원하는 버튼에 추가할 수 있도록 설계하였다.

**Camera Functions**
- *Primary Button* : Record; Start, Stop
- *Secondary Button* : Record; Cancel
- *Menu Button* : Control Detail Functions
- *Joystick* : Zoom; In/Out, On/Off
- *Focus* : On/Off
- *Stabilizer* : Always On
- *Clapper Board* : Auto Update Info
- *Preview Screen* : Show Camera State

### a. 구조

<kbd>FilmCamera</kbd>의 구조를 만들 때 '**Dependency Cycle**'을 항상 염두에 두고 종속성을 끊어 주기 위해 노력했다. 따라서 <kbd>FilmCameraController</kbd>에서 카메라의 모든 기능을 참조하고 있는 것이 아니라 카메라의 기능들이 컨트롤러를 참조하고 각 기능이 알맞은 콜백함수에 구독하는 방식으로 설계하였다. 이렇게 설계함으로써 <kbd>FilmCameraController</kbd>가 카메라 기능들로부터 자유로워지고 원하는 기능을 코드의 수정 없이 추가하거나 뺄 수 있게 되었다. 

##### Delegate Functions

{% highlight cs %}
#region Delegates
public delegate void FilmCamButton();
public delegate void FilmCamJoystick(float val);

public FilmCamButton PrimaryButtonAction;
public FilmCamButton SecondaryButtonAction;
public FilmCamButton MenuButtonAction;
public FilmCamJoystick JoystickAction;
#endregion
{% endhighlight %}

##### FilmCamera Sequence Diagram

@startmermaid
sequenceDiagram
    participant FCC as FilmCameraController
    participant CZ as CameraZoom
    participant CR as CameraRecord
    participant CB as ClapperBoard
    participant CS as CameraStabilizer
    participant CF as CameraFocus
    participant C as Camera
    
    activate FCC
    activate C
    activate CS
    CS->>C: Perform Stabilize
    FCC-->>CF: Auto focus On/Off Invoke
        activate CF
        alt auto focus on
            CF->>C: Activate Focus
        else auto focus off
            CF->>C: Deactivate Focus
            deactivate CF
        end
    FCC-->>CZ: OnValueChange Invoke
    activate CZ
        alt negative value
            CZ->>C: Perform Zoom In
        else positive value
            CZ->>C: Perform Zoom Out
            deactivate CZ
        end
    par [Not Recording]
        FCC-->>+CR: OnButtonDown Invoke
        CR->>+C: Start Recording
        deactivate CR
        C-->>+CB: Clap When Recording
        deactivate C
    and [Recording]
        deactivate CB
        FCC-->>+CR: OnButtonDown Invoke
        CR->>C: Stop Recording
        deactivate CR
    end
    deactivate CS
    deactivate C
    deactivate FCC
@endmermaid

### b. CameraZoom
<figure>
<img src="/project-8pmStudios/zoomControl.gif" alt="Smooth Zoom Control">
<figcaption>Fig 2. SmoothZoom Control.</figcaption>
</figure>

카메라 줌은 <kbd>FixedLensZoom</kbd>, <kbd>SmoothZoom</kbd> 두가지 모드를 제공한다.\
조이스틱을 **Z축** 방향으로만 움직일 수 있고 당기면 줌인 밀면 줌아웃이 된다.

##### CameraZoom.cs

{% highlight cs %}
#region Subscribed Functions
/// <summary>
/// Perform smooth zoom.
/// </summary>
/// <param name="dir"></param>
private void Zoom(float dir)
{
    var fov = _cam.fieldOfView + dir * zoomAmount * Time.deltaTime;
    _cam.fieldOfView = Mathf.Clamp(fov,
        lensPresets.Lens[lensPresets.Lens.Length - 1].FoV,
            lensPresets.Lens[0].FoV);
}

/// <summary>
/// Perform fixed lens zoom.
/// </summary>
/// <param name="dir"></param>
private void FixedLens(float dir)
{
    var fov = _cam.fieldOfView + dir * zoomAmount * Time.deltaTime;
    var fovClamp = Mathf.Clamp(fov,
        lensPresets.Lens[lensPresets.Lens.Length - 1].FoV,
            lensPresets.Lens[0].FoV);
    var index = lensPresets.GetLensIndex(fovClamp);
    _cam.fieldOfView = lensPresets.Lens[index].FoV;
}
#endregion
{% endhighlight %}

두 함수 모두 VR 조이스틱으로부터 받은 방향값으로 새로운 fov 값을 계산하여 카메라 `.fieldOfView`에 넣어준다.

<kbd>FixedLensZoom</kbd>같은 경우 '**Scriptable Object**'인 <kbd>lensPreset</kbd>에 미리 정의된 fov값과 비교하여 가장 값이 비슷한 렌즈의 fov를 반환하는 형태이다.

##### LensPresets.cs

{% highlight cs %}
#region Initialize
/// <summary>
/// This function should be called on Start.
/// </summary>
public void CreateDefaultLens()
{
    if (!_isInitialized)
    {
        List<LensPreset> defaults = new List<LensPreset>();
        defaults.Add(new LensPreset() { Name = "21mm", FoV = 60f });
        defaults.Add(new LensPreset() { Name = "35mm", FoV = 38f });
        defaults.Add(new LensPreset() { Name = "58mm", FoV = 23f });
        defaults.Add(new LensPreset() { Name = "80mm", FoV = 17f });
        defaults.Add(new LensPreset() { Name = "125mm", FoV = 10f });
        _lens = defaults.ToArray();
        _isInitialized = true;
    }
}
#endregion
{% endhighlight %}

계산된 값이 `_threshold`보다 낮다면 렌즈를 찾았다고 판단하여 해당 인덱스를 반환한다.

##### LensPresets.cs

{% highlight cs %}
/// <summary>
/// Get the index of the lens preset that matches the fov value.
/// </summary>
/// <param name="fov"></param>
/// <returns></returns>
public int GetLensIndex(float fov)
{
    for (int i = 0; i < _lens.Length; ++i)
    {
        if (Mathf.Abs(_lens[i].FoV) - Mathf.Abs(fov) <= _threshold)
            return i;
    }
    return -1;
}
{% endhighlight %}

### c. CameraFocus

<figure>
<img src="/project-8pmStudios/camAutoFocus.gif" alt="Camera Auto Focus">
<figcaption>Fig 3. Camera Auto Focus.</figcaption>
</figure>

포커스 기능은 중앙점에서 바라보고 있는 물체에 자동으로 초점을 맞춘다. 포커스 모드에는 <kbd>매뉴얼 포커스</kbd> 모드와 <kbd>오토 포커스</kbd> 모드가 있다.

<kbd>autoFocus</kbd> 기능은 메뉴를 통해 켜거나 끌 수 있다.

##### CameraFocus.cs

{% highlight cs %}
#region Subscribed Functions
/// <summary>
/// 매뉴얼 포커스, 오토 포커스 모두 같은 함수로 실행된다. 
/// </summary>
private void Focus()
{
    StartCoroutine(Focusing());
}

private IEnumerator Focusing()
{
    if (_isCoroutineRunning) yield break;
    _isCoroutineRunning = true;

    var ray = new Ray(_cam.transform.position, _cam.transform.forward);

    if (Physics.SphereCast(ray, rayRadius, out var hit, rayLength))
    {
        var hitDst = Vector3.Distance(_cam.transform.position, hit.point);
        var elapsedTime = 0f;

        while (elapsedTime <= focusTime)
        {
            var lerpVal = Mathf.Lerp(_dof.focusDistance.value, hitDst,
                focusAc.Evaluate(elapsedTime / focusTime));
            _dof.focusDistance.value = lerpVal;
            elapsedTime += Time.fixedDeltaTime;
            yield return null;
        }
        _dof.focusDistance.value = hitDst;
    }
    _isCoroutineRunning = false;
}
{% endhighlight %}

포커스 기능은 `.SphereCast`를 쏴서 맞춘 대상까지의 거리를 _dof의 `.focusDistance.value`에 넣어준다. 부드럽게 전환을 주기 위해 `Mathf.Lerp`와 AnimationCurve를 사용하였다.

### d. CameraStabilizer

<figure>
<img src="/project-8pmStudios/cameraStabilizer.gif" alt="camera stabilizer">
<figcaption>Fig 4. Camera Stabilizer.</figcaption>
</figure>

VR 컨트롤러는 사용자의 움직임을 정확하게 따라가야 하므로 작은 움직임도 다 파악한다. 하지만 사용자 컨트롤러의 민감도는 카메라를 들고 촬영 시 치명적일 수 있다. <kbd>CameraStabilizer</kbd>는 이러한 손 떨림 문제를 해결해준다.

##### CameraStabilizer.cs

{% highlight cs %}
[Header("Camera"), SerializeField]
private Transform filmCam = null;
[SerializeField]
private Transform camAnchor = null;
[Header("Stabilizer"), SerializeField]
private float positionStabilizeValue = 0.6f;
[SerializeField]
private float rotationStabilizeValue = 0.6f;

void Update()
{
    var positionOffset = Vector3.Distance(camAnchor.position, filmCam.position) + 1;
    filmCam.position = Vector3.Lerp(filmCam.position, camAnchor.position,
        positionOffset * Time.deltaTime * positionStabilizeValue * 10);

    var rotationOffset = Quaternion.Angle(camAnchor.rotation, filmCam.rotation);
    filmCam.rotation = Quaternion.Lerp(filmCam.rotation, camAnchor.rotation,
        rotationOffset * Time.deltaTime * rotationStabilizeValue / 1.5f);
}
{% endhighlight %}

'**Unity Camera**'의 포지션값과 로테이션값은 설정한 `camAnchor`로 포지션은 거리에 따라, 로테이션은 각도에 따라 Lerp한 값이 적용된다.

### e. ClapperBoard & Preview Screen

<figure>
<img src="/project-8pmStudios/camScreen.gif" alt="camera screen info">
<figcaption>Fig 5. Camera screen info.</figcaption>
</figure>

<kbd>ClapperBoard</kbd>는 영상 편집자에게 카메라의 <kbd>PreviewScreen</kbd>은 촬영 감독에게 아주 중요한 정보를 제공한다.

녹화가 시작되면 <kbd>ClapperBoard</kbd>가 영상 정보와 함께 알아서 화면에 표시되는 것을 확인할 수 있다. <kbd>PreviewScreen</kbd>에는 렌즈 정보, 촬영 날짜/시간, 테이크 정보, 그리고 녹화 상태를 표시한다.

##### ClapperBoardController.cs

{% highlight cs %}
#region Subscribed Functions
/// <summary>
/// 녹화 시작시 한번 호출되고 기록 내용을 업데이트한다.
/// </summary>
private void Clap()
{
    if (!_canClap) return;
    gameObject.SetActive(true);

    studioInfo.text = studioPreset.Studio;
    productionInfo.text = studioPreset.Production;
    directorInfo.text = studioPreset.Director;
    dateInfo.text = DateTime.UtcNow.ToLocalTime().ToString("yyyy.MM.dd");
    sceneInfo.text = studioPreset.Scene;
    takeInfo.text = studioPreset.TakeCount.ToString();

    Invoke("Action", delayTime);
}
private void Action() => _animator.SetTrigger("action");
{% endhighlight %}

<kbd>ClapperBoard</kbd> 정보 대부분은 `studioPreset` 이라는 **Scriptable Object**에 기록된 정보를 가져온다. 녹화가 시작되면 작은 딜레이 이후에 슬레이트를 치게 된다. 

## 2. ControlDeskSystem

<figure>
<img src="/project-8pmStudios/controlDeskButtons.png" alt="ControlDeskSystem Buttons">
<figcaption>Fig 6. ControlDeskSystem Buttons.</figcaption>
</figure>

<kbd>ControlDeskSystem</kbd>는 촬영감독이 한곳에서 여러 대 카메라를 한 번에 제어하거나 찍은 영상을 확인하는 목적으로 사용된다. <kbd>FilmCamera</kbd>와 마찬가지로 너무 많은 버튼을 한꺼번에 제공하면 사용가 햇갈려 하고 사용자 경험이 더 떨어질 수 있기 때문에 같은 버튼을 모드 스위치를 통해 다른 기능을 수행하게끔 구현하였다.

<kbd>ControlDeskSystem</kbd>는 두가지 모드가 있고 모드에 따라 같은 버튼이 다른 기능을 하게 된다.

**CameraControlMode**
- *Mode Switch Button* : <kbd>VideoControlMode</kbd>로 변경
- *Previous Button* : 이전 카메라의 프리뷰 스크린을 화면에 띄운다
- *Next Button* : 다음 인덱스의 카메라 프리뷰 스크린을 화면에 띄운다
- *Primary Button* : 선택된 카메라 녹화/중지
- *Secondary Button* : 현재 카메라 선택/해제

**VideoControlMode**
- *Mode Switch Button*: <kbd>CameraControlMode</kbd>로 변경
- *Previous Button*: 저장되어 있는 이전 영상을 재생
- *Next Button*: 저장되어 있는 다음 인덱스의 영상을 재생
- *Primary Button*: 영상 재생/일시정지, 영상 삭제 확인
- *Secondary Button*: 영상 삭제, 영상 삭제 취소

### a. 구조

<kbd>ControlDeskSystem</kbd>의 구조는 <kbd>FilmCamera</kbd>의 구조와 비슷하다. <kbd>ControlDeskSystem</kbd>의 기능은 모드가 변경될 때마다 콜백함수에 구독되는 기능이 바뀌게 된다. 

##### Delegate Funcitons

{% highlight cs %}
#region Delegates
public delegate void ModeControlDeskButton(ControlMode mode);
public delegate void ControlDeskButton();

public ModeControlDeskButton ModeSwitchAction = null;
public ControlDeskButton PreviousAction = null;
public ControlDeskButton NextAction = null;
public ControlDeskButton PrimaryAction = null;
public ControlDeskButton SecondaryAction = null;
#endregion
{% endhighlight %}

##### ControlDeskSystem Sequence Diagram

@startmermaid
sequenceDiagram
    participant CDS as ControlDeskSystem
    participant FCM as FilmCameraManager
    participant VPM as VideoPlayerManager
    participant OS as OutputScreen
    participant C1 as Camera_1

    activate CDS
    activate OS
    activate C1
    C1->>CDS: If Active Let Control Desk Know
    par [previous state : VideoControlMode]
        CDS-->>+FCM: Mode OnButtonDown
        FCM->>FCM: Subscribe Callbacks
        deactivate FCM
        CDS-->>+VPM: Mode OnButtonDown
        VPM->>VPM: Unsubscribe Callbacks
        deactivate VPM
    and [previous state : CameraControlMode]
        CDS-->>+FCM: Mode OnButtonDown
        FCM->>FCM: Unsubscribe Callbacks
        deactivate FCM
        CDS-->>+VPM: Mode OnButtonDown
        VPM->>VPM: Subscribe Callbacks
        deactivate VPM
    end
    alt current state : CameraControlMode
        CDS-->>+FCM: Previous OnButtonDown
        FCM->>OS: Show Previous Camera Screen
        deactivate FCM
    else current state : VideoControlMode
        CDS-->>+VPM: Previous OnButtonDown
        VPM->>OS: Show Previous Recorded Video
        deactivate VPM
    end
    alt current state : CameraControlMode
        CDS-->>+FCM: Next OnButtonDown
        FCM->>OS: Show Next Camera Screen
        deactivate FCM
    else current state : VideoControlMode
        CDS-->>+VPM: Next OnButtonDown
        VPM->>OS: Show Next Recorded Video
        deactivate VPM
    end
    alt current state : CameraControlMode
        CDS-->>+FCM: Primary OnButtonDown
        par [current state : not recording]
            FCM->>C1: Record Selected
        and [current state : recording]
            FCM->>C1: Stop Record Selected
            deactivate FCM
        end
    else current state : VideoControlMode
        CDS-->>+VPM: Primary OnButtonDown
        par [current state : paused]
            VPM->>OS: Play Video
        and [current state : playing]
            VPM->>OS: Pause Video
            deactivate VPM
        end
    end
    alt current state : CameraControlMode
        CDS-->>+FCM: Secondary OnButtonDown
        par [current state : selected]
            FCM->>C1: Deselect Camera
        and [current state : not selected]
            FCM->>C1: Select Camera
            deactivate FCM
        end
    else current state : VideoControlMode
        CDS-->>+VPM: Secondary OnButtonDown
        par [delete mode : on]
            VPM->>OS: Cancel Delete Mode
        and [delete mode : off]
            VPM->>OS: Turn On Delete Mode
            deactivate VPM
        end
    end
    deactivate OS
    deactivate C1
    deactivate CDS
@endmermaid

### b. FilmCameraManager

<kbd>ControlDeskSystem</kbd>에서 모드 변경 신호가 들어오면 구독된 `OnModeChange()` 함수가 호출되고 모드가 변경될 때마다 콜백함수에 구독하거나 취소하게 된다. 

##### FilmCameraManager.cs

{% highlight cs %}
private void OnModeChange(ControlMode mode)
{
    _currentMode = mode;
    if (_currentMode.Equals(ControlMode.CameraViewer))
    {
        _deskController.PreviousAction += ShowPreviousCam;
        _deskController.NextAction += ShowNextCam;
        _deskController.PrimaryAction += RecordSelectedCam;
        _deskController.SecondaryAction += SelectCam;
    }
    else
    {
        _deskController.PreviousAction -= ShowPreviousCam;
        _deskController.NextAction -= ShowNextCam;
        _deskController.PrimaryAction -= RecordSelectedCam;
        _deskController.SecondaryAction -= SelectCam;
    }
}
{% endhighlight %}

<kbd>FilmCameraManager</kbd>는 여러 대의 카메라를 관리하는 역할을 가지고 있다.

<figure>
<img src="/project-8pmStudios/switchCam.gif" alt="Switch Camera View">
<figcaption>Fig 7. Switch Camera View.</figcaption>
</figure>

##### FilmCameraManager.cs

{% highlight cs %}
private void ShowScreen(int index)
{
    if (_filmCameras.Count > index)
    {
        var sc = _filmCameras[index].GetComponentInChildren<ScreenController>();
        _viewScreen.GetComponent<Renderer>().material.mainTexture = sc.NotRecordingRt;
    }
}
{% endhighlight %}

<kbd>NextButton</kbd>이나 <kbd>PreviousButton</kbd>이 호출되면 다음 인덱스를 `ShowScreen()`에 넘겨준다. 넘겨받은 인덱스에 해당하는 카메라의 **RenderTexture를** <kbd>ScreenController</kbd>로 부 터 받아온다. 받아온 **RT**를 화면 메테리얼에 넣어준다.

### c. VideoPlayerManager

<figure>
<img src="/project-8pmStudios/watchSaved.gif" alt="Watch Saved Video">
<figcaption>Fig 8. Watch Saved Video.</figcaption>
</figure>

<kbd>VideoPlayerManager</kbd>는 찍은 영상을 바로 확인할 수 있게 해준다.

##### VideoPlayerManager.cs

{% highlight cs %}
/// <summary>
/// Update video name list.
/// 영상이 저장되면 호출된다.
/// </summary>
public void UpdateVideoList()
{
    var fileNames = Directory.GetFiles(CAPTURE_PATH, "*.mp4");
    Array.Sort(fileNames);

    if (_videoNames == null)
    {
        _videoNames = new List<string>();
    }
    else if (_videoNames.Count > 0)
    {
        _videoNames.Clear();
    }

    foreach (var name in fileNames)
    {
        _videoNames.Add(Path.GetFileNameWithoutExtension(name));
    }
}
{% endhighlight %}

영상 목록은 영상 이름으로 관리를 한다. 새로운 영상이 찍히면 `UpdateVideoList()`가 호출되고 항상 최신 리스트를 가지게 된다.

##### VideoPlayerManager.cs

{% highlight cs %}
private void PlayPause()
{
    if (_videoPlayer.isPlaying)
        _videoPlayer.Pause();
    else
        _videoPlayer.Play();
}

private void PlayNew(int index)
{
    if (_videoPlayer.isPlaying)
        _videoPlayer.Stop();
    if (_videoNames.Count > index)
        StartCoroutine(LoadVideo(_videoNames[index]));
}

private IEnumerator LoadVideo(string name)
{
    if (_isLoading) yield break;
    _isLoading = true;
    _videoPlayer.url = CAPTURE_PATH + name + ".mp4";
    _isLoading = false;
}
{% endhighlight %}

현재 재생 영상은 인덱스로 관리된다. 새로운 영상을 불러올 때는 인덱스에 해당하는 영상 이름으로 찾는다. 

## 3. CameraPathCreator

<figure>
<img src="/project-8pmStudios/pathCreator.png" alt="Path Creator">
<figcaption>Fig 9. Path Creator.</figcaption>
</figure>

<kbd>CameraPathCreator</kbd>는 부드러운 곡선을 따라 촬영을 할 때 손쉽게 촬영할 수 있도록 보조한다. 이러한 작업은 작은 디테일 하나하나가 중요하다. 따라서 <kbd>Anchor Point</kbd>와 <kbd>Control Point</kbd>를 VR 상에서 잡아서 원하는 곳에 배치할 수 있게 하였다. <kbd>Cart</kbd>에 카메라를 장착하고 버튼을 누르면 설정한 속도로 <kbd>Cart</kbd>가 움직이게 된다.

### a. 구조

<kbd>CameraPathCreator</kbd>는 <kbd>Object Pool</kbd>에서 미리 만들어진 **Point**들을 배치하고 <kbd>PathViewer</kbd>가 **Point**들을 이어서 **Path**를 만든다. 

##### CameraPathCreator Sequence Diagram

@startmermaid
sequenceDiagram
    participant OP as ObjectPool
    participant PC as PathCreator
    participant PD as PointDetector
    participant PV as PathViewer

    activate OP
    activate PC
    OP->>OP: Create Pool
    PC-->>+PV: if point > 1 : Update Viewer
    alt point not detected
        PC-->>+OP: Has Input : Request Point
        OP->>-PC: Return Point
    else point detected
        PD-->>PC: OnTriggerEnter With Point
        PC-->>+OP: Has Input : Remove Point
        OP->>-PC: Remove Point
    end
    deactivate PV
    deactivate PC
    deactivate OP
@endmermaid

### b. BezierCurves

<figure>
<img src="/project-8pmStudios/controlPath.gif" alt="Bezier Curve">
<figcaption>Fig 10. Bezier Curve.</figcaption>
</figure>

##### BezierCurves.cs

<kbd>CameraPathCreator</kbd>의 핵심인 **BezierCurve**이다. [위키](https://en.wikipedia.org/wiki/Bézier_curve)의 공식을 참고해서 static 클래스를 만들어 사용하였다.

{% highlight cs %}
public static Vector3 LinearBezierCurves(Vector3 a, Vector3 b, float t)
{
    return a + t * (b - a);
}

public static Vector3 QuadraticBezierCurves(Vector3 a, Vector3 b, Vector3 c, float t)
{
    return Mathf.Pow(1 - t, 2) * a + 2 * (1 - t) * t * b + Mathf.Pow(t, 2) * c;
}

public static Vector3 CubicBezierCurves(Vector3 a, Vector3 b, Vector3 c, Vector3 d, float t)
{
    return Mathf.Pow(1 - t, 3) * a + 3 * Mathf.Pow(1 - t, 2) * t * b +
        3 * (1 - t) * Mathf.Pow(t, 2) * c + Mathf.Pow(t, 3) * d;
}
{% endhighlight %}

### c. PathCreator

<figure>
<img src="/project-8pmStudios/path.gif" alt="Create Path">
<figcaption>Fig 11. Create Path.</figcaption>
</figure>

<kbd>PathCreator</kbd>는 <kbd>CameraPathCreator</kbd>의 중심축으로 <kbd>Object Pool</kbd>와 <kbd>PathViewer</kbd> 사이에서 컨트롤 하는 작업을 한다. 

##### PathCreator.cs

{% highlight cs %}
/// <summary>
/// Gets called when an active Path Point is not detected
/// and when the VR button is pressed.
/// </summary>
public void AddPoint()
{
    if (!_detector.IsTriggerStay)
    {
        var point = _pool.ActivatePoint(spawnPoint.position, spawnPoint.rotation);
        if (point != null)
        {
            _points.Add(point);
        }
        if (_points.Count > 1)
        {
            _cart.transform.position = _points[0].position;
            _cart.SetActive(true);
        }
    }
}

/// <summary>
/// Gets called when an active Path Point is detected
/// and when the VR button is pressed.
/// </summary>
/// <param name="point"></param>
public void RemovePoint(Transform point)
{
    int index = _points.FindIndex(p => p == point);
    if (index != -1)
    {
        _pool.TerminatePoint(_points[index]);
        _points.RemoveAt(index);
    }

    if (_points.Count < 2)
    {
        _cart.SetActive(false);
    }
}
{% endhighlight %}

**Point**의 생성은 <kbd>PointDetector</kbd>에 **Point**가 트리거되는것이 없을때만 가능하다. 

**Point**의 삭제는 <kbd>PointDetector</kbd>로 부터 건네 받은 **Point**의 **Transform**의 인덱스를 찾아 <kbd>ObjectPool</kbd>에서 `.TerminatePoint()` 과정을 거친다.

### d. PathPool

<kbd>PathPool</kbd>은 Stack을 이용했다. **has-a** 방식으로 **Object Pool**을 생성하여 **MonoBehaviour**의 life cycle을 타지 않게 하였다.

##### PathPool.cs

{% highlight cs %}
public PathPool(MonoBehaviour mono, GameObject spawnObject, int pathCount)
{
    InitVariables(mono, spawnObject, pathCount);
    CreatePool();
}

private void CreatePool()
{
    _poolRoot = GameObject.Find("PathPool");
    if (_poolRoot == null)
    {
        _poolRoot = new GameObject("PathPool");
    }
    for (int i = 0; i < _pathCount; i++)
    {
        var point = MonoBehaviour.Instantiate(_spawnObject, _mono.transform.position,
            Quaternion.identity, _poolRoot.transform);
        point.SetActive(false);
        _pathPool.Push(point);
    }
}
{% endhighlight %}

### e. PathViewer

<figure>
<img src="/project-8pmStudios/pathCart.gif" alt="Camera Path + Cart">
<figcaption>Fig 12. Camera Path + Cart.</figcaption>
</figure>

<kbd>PathViewer</kbd>는 처음 생성자에서 넘겨받은 **Point** 리스트 레퍼런스를 가지고 **LineRenderer**를 통해 선을 그린다.

##### PathViewer.cs

{% highlight cs %}
/* Cubic Bezier Curve */
case var expression when points > 3:
    t = 0f;
    for (int i = 0; i < SegmentCount(); i++)
    {
        var cp1 = _points[i * 3 + 1].GetComponent<Renderer>();
        var cp2 = _points[i * 3 + 2].GetComponent<Renderer>();
        cp1.material = _controlPointMat;
        cp2.material = _controlPointMat;

        Vector3[] segment = GetPointsInSegment(i);
        for (int j = 0; j < _lr.positionCount; j++)
        {
            _lr.SetPosition(j, BezierCurves.CubicBezierCurves(
                segment[0], segment[1], segment[2], segment[3], t));
            t += (1 / (float)_lr.positionCount);
        }
    }
    break;
{% endhighlight %}

`.GetPointsInSegment()`는 **Point**를 3개씩 겹치도록 반환 한다.

<br />