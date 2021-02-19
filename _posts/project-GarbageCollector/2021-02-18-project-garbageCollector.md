---
layout: post
title: Garbage Collector
date: 2021-02-18 09:29:20 +0700
modified: 2021-02-18 21:49:47 +07:00
categories: Projects
tags: [RAPA, Project, PC]
description: 부유 쓰레기를 수거하는 보트 게임.
---

<figure>
<img src="/project-garbageCollector/GC_Cover.png" alt="Garbage Collector Cover">
</figure>

**팀명:** CodeSurfers\
**팀원:** 정은채, 김현우\
**개발 환경:** Unity 2019.4 URP, Visual Studio, GitLab\
**제작 기간:** 2020.08.18 ~ 2021.09.15\
**YouTube:** [Garbage Collector Devlog](https://www.youtube.com/watch?v=nUcayipipwk&t=77s)
<br />

<hr>

# Index

**1. [<kbd> 프로젝트 소개 </kbd>](#프로젝트-소개)**
- [<kbd> 목표 </kbd>](#목표)
- [<kbd> 스토리 </kbd>](#스토리)
- [<kbd> 구조 </kbd>](#구조)

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

### 목표
<kbd>Garbage Collector</kbd>는 한 달 동안 진행한 프로젝트이고 **타겟 콘텐츠**를 정하고 최대한 똑같이 만드는걸 목표로 하였다.

<figure>
<img src="/project-garbageCollector/target.png" alt="Target Game">
<figcaption>Fig 1. Target Game.</figcaption>
</figure>

타겟은 보트 레이싱 게임인 '**더 크루 2**'이다. 이 게임에서 최대한 따라 하려고 한 몇 가지 포이트가 있다.
- 잔잔한 바다
- 물 이펙트
- 통통 튀는 보트
- 스피디한 느낌

추가로 바다에 떠다니는 부유 쓰레기를 청소해서 해양 동물들을 돕자는 아이디어도 넣었다.

### 스토리

<br />
<figure-round>
<img src="/project-garbageCollector/willy.png" alt="Willy" width="180px">
<figcaption>Willy</figcaption>
</figure-round>

> **행복하게 헤엄치던 고래 윌리네 바다가 쓰레기들로 오염되었다.**\
> **슬퍼하는 윌리를 위해 보트를 운전하여 정해진 시간 내에 최대한 많은 부유 쓰레기를 건져내자!**

### 구조

<kbd>Garbage Collector</kbd>은 심플한 아케이드 게임의 구조를 가진다. 타임 아웃이 되면 기록이 남고 다시 플레이하는 식으로 **리플레이성**이 짙다.

##### Main Flow

@startmermaid
stateDiagram-v2
    [*] --> Active
    state Active {
        [*] --> MainMenu
        MainMenu --> [*] : quit
        MainMenu --> GameOn : clickPlay
        GameOn --> Result : timeOut
        Result --> GameOn : playAgain
        Result --> [*] : quit
    }
    Active --> [*]
@endmermaid

### 역할 분담 및 개발 일정
##### 팀 구성원


##### 개발 일정
@startmermaid
gantt
    dateFormat  YYYY-MM-DD
    section 프로토타입
    타겟 선정    :proto1, 2020-08-18, 1d
    매쉬 생성, 웨이브 적용    :proto3, after proto1, 6d
    유니티 웨이브 적용    :proto4, after proto3, 2d
    하이트맵 생성    :proto5, after proto4, 1d
    터레인 생성    : proto6, after proto5, 3d
    section 알파
    가비지 스폰    :alpha1, 2020-09-01, 1d
    후크 연결    :alpha2, after alpha1, 4d
    게임 시스템    :alpha3, after alpha2, 4d
    section 베타
    UI    :beta1, 2020-09-08, 5d
    Sound & Effect    :beta2, after beta1, 3d
@endmermaid

# 프로젝트 구현

## 1. Water System

<figure>
<img src="/project-garbageCollector/unityWaterSystem.png" alt="Unity Wave">
<figcaption>Fig 2. Unity Water System.</figcaption>
</figure>

물을 표현하기 위해 다양한 시도를 해보았다. 폴리곤 수가 많은 플레인을 생성해 **Vertex**의 높이를 조정하거나 **Shader Graph** 만을 이용해 웨이브를 생성했다. 하지만 물을 표현하는것은 어렵고 정말 많은 요소들이 필요 하다는 것을 알게 되었다.
- *Wave* : 거스너 웨이브
- *Reflection* : 반사
- *Refraction* : 굴절
- *Caustic* : 바닥에 커스틱 효과
- *Frasnel* : 카메라 뷰 각에 따라 반사 or 굴절
- *Scattering* : 산란 (가까운 바다)
- *Absorption* : 흡수 (먼 바다) 어두워 보임

### a. Mesh Base

<figure>
<img src="/project-garbageCollector/meshWave.gif" alt="Mesh Base Wave">
<figcaption>Fig 3. Mesh Base Wave.</figcaption>
</figure>

**Mesh**를 런타임 중 생성해서 [Octave Wave를 적용하는 방식](https://www.youtube.com/watch?v=_Ij24zRI9J0&t=44s)이다. **Octave Wave**를 적용할 때는 `Mathf.PerlinNoise()`의 반환 값을 버텍스의 높이에 적용하게 되는데 매 프레임 위치 갚을 바꿔주고 **Mesh**의 노멀을 다시 계산해 줘야 해서 연산량이 많다.
**Mesh**가 폴리곤의 수가 적으면 문제가 되지 않지만, 폴리곤의 수를 조금만 늘려도 프레임 드랍이 발생해 바다를 표현하기에는 적절하지 않았다.

### b. Shader Graph Base

| <img src="/project-garbageCollector/wave.1.gif" alt="Wave 1"> | <img src="/project-garbageCollector/wave.2.gif" alt="Wave 2"> |
| <img src="/project-garbageCollector/wave.3.gif" alt="Wave 3"> | <img src="/project-garbageCollector/wave.4.gif" alt="Wave 4"> |

<figure><figcaption>Fig 4. Shader Graph Base Wave.</figcaption></figure>

**Shader Graph**를 이용해 물을 표현하는 방식은 대부분 비슷했다. 서로 다른 노멀맵 혹은 노이즈맵을 컴바인 시켜 시간에 흐름에 따라 offset을 주는 방식으로 물결을 만들었다. 반사효과와 산란 효과도 표현할 수 있었으나 무언가 많이 부족한 느낌이 있었다.

### c. Unity Water System

<figure>
<img src="/project-garbageCollector/unity wave.gif" alt="Unity Wave">
<figcaption>Fig 4. Unity Wave.</figcaption>
</figure>

결국 마지막에는 [Unity BoatAttack](https://github.com/Unity-Technologies/BoatAttack) 오픈소스의 <kbd>Water System</kbd>을 적용하였다. 앞서 말한 물을 표현하기 위한 요소들은 다 들어가있는 아름다운 물이었다. 물리적으로 인터렉션이 불가능했지만 <kbd>Buoyancy System</kbd>도 내장되어 있었다. 무엇보다도 **Unity Jobsystem**과 **Burst Compiler**를 이용해 모바일에서도 높은 퀄리티의 물 표현과 인터렉션이 가능하다는 게 흥미로웠다.

<figure>
<img src="/project-garbageCollector/waterMesh.png" alt="Water Mesh">
<figcaption>Fig 5. Water Mesh.</figcaption>
</figure>

최적화를 위해 원 형태로 중앙으로 갈수록 LOD가 높은 매쉬를 사용했고 플레이어를 계속 따라다니면 티가 나기 때문에 일정 거리 이상 벌어지면 그때 플레이어 위치로 순간이동 시킨다.

## 2. Procedural Terrain

<figure>
<img src="/project-garbageCollector/terrain.PNG" alt="Procedural Terrain">
<figcaption>Fig 5. Procedural Terrain.</figcaption>
</figure>

<kbd>Procedural Terrain</kbd>은 god like coder **Sebastian Lague**의 [Procedural Landmass Generation](https://www.youtube.com/watch?v=wbpMiKiSKm8&list=PLFt_AvWsXl0eBW2EiBtl_sxmDtSgZBxB3)의 시리즈의 일부분을 적용하였다.

### a. Height Map

| <img src="/project-garbageCollector/noise map.gif" alt="Noise Map"> | <img src="/project-garbageCollector/noise map depth colored.gif" alt="Colored"> |

<figure><figcaption>Fig 6. Height Map.</figcaption></figure>

**Height Map**은 0(Black)과 1(White) 사이의 값으로 높이를 표현한다. 일반적인 노이즈가 아니라 **Perline Noise**를 주면 좀 더 organic 한 느낌을 줘서 산맥이나 물을 표현하는 데 많이 사용되는 것 같다.
생성된 **Height Map**에 값에 따라 색을 더하면 오른쪽과 같은 결과가 나온다.

### b. Generate Mesh

<figure>
<img src="/project-garbageCollector/mesh height.gif" alt="Generated Mesh">
<figcaption>Fig 7. Generated Mesh.</figcaption>
</figure>

**Mesh**의 vertex 를 **Height Map**의 높이값에 따라 조정하면 산맥 느낌을 만들 수 있다.

## 3. Prototype Result

<figure>
<img src="/project-garbageCollector/prototype.1.gif" alt="Prototype Result">
<figcaption>Fig 7. Prototype Result.</figcaption>
</figure>

여기까지가 **Prototype**의 결과다.

## 4. Garbage & Spawner

<figure>
<img src="/project-garbageCollector/floatingGarbage.png" alt="Floating Garbage">
<figcaption>Fig 8. Floating Garbage.</figcaption>
</figure>

<kbd>Garbage</kbd>는 플레이어가 <kbd>Grappling Hook</kbd>으로 건져내면 점수가 올라간다. 때문에 그냥 둥둥 떠다니면 너무 쉽고 재미가 없다.
조금의 리플레이성을 주기 위해 다음과 같은 **Life Cycle**과 **State**를 주었다.

### a. 구조

##### Life Cycle

@startmermaid
sequenceDiagram
    participant OP as Object Pool
    participant S as SpawnerOne
    participant G as Garbage
    participant P as Player
    
    activate P
    activate OP
    OP->>OP : Create Pool
    OP->>G : Deactivate
    deactivate OP
    S-->>+OP : Request Object to Spawn
    OP->>+G : Activate and Inject SpawnerOne Ref
    OP->>-S : Return Object Ref
    activate S
    S->>S : Increase Count
    S->>G : Set Random Point
    deactivate S
    alt got caught
        P-->>G : Catch!
        G-->>S : Decrease Count
        G-->>+OP : Deactivate Process
        OP-->>-G : Deactivate
        deactivate G
    end
    deactivate P
@endmermaid

<kbd>Garbage</kbd>는 **Spawner**에 의해 스폰된 **Spawner**의 레퍼런스를 주입 받고 활성화된다. **Spawner**는 미리 지정된 수만 자신의 영역에 무작위 스폰을하고 `_count`로 활성화된 <kbd>Garbage</kbd>의 수를 관리하고 있다. 플레이어가 <kbd>Garbage</kbd>를 잡으면 잡힌 <kbd>Garbage</kbd>는 참조하고 있던 **Spawner**의 count를 하나 줄이고 비활성화된다.

##### FSM

@startmermaid
stateDiagram-v2
    [*] --> Active : spawn
    state Active {
        [*] --> FloatAround : activate
        FloatAround --> SwimAway : playerDetected
        SwimAway --> FloatAround : playerNotDetected
        FloatAround --> [*] : getCaught
        SwimAway --> [*] : getCaught
    }
    Active --> [*] : terminate
@endmermaid

<kbd>Garbage</kbd>는 구 형태의 <kbd>Detect Range</kbd>가 있고 플레이어가 거리 안으로 들어오면 플레이어 **반대 방향**으로 도망친다.

### b. Garbage Pool

<figure>
<img src="/project-garbageCollector/objectPool.png" alt="Object Pool">
<figcaption>Fig 9. Garbage Pool.</figcaption>
</figure>

<kbd>Garbage Pool</kbd>은 딕셔너리와 큐로 구현하였다.

##### ObjectPool.cs

{% highlight cs %}
[System.Serializable]
public class Pool
{
    [SerializeField] private SpawnObjectTag tag = SpawnObjectTag.Garbage;
    [SerializeField] private List<GameObject> prefabs = null;
    [SerializeField] private int size = 0;

    public SpawnObjectTag Tag { get => tag; set => tag = value; }
    public List<GameObject> Prefabs { get => prefabs; set => prefabs = value; }
    public int Size { get => size; set => size = value; }
}
{% endhighlight %}

`prefabs`는 여러가지 <kbd>Garbage Pool</kbd>를 담기 위해 리스트로 만들었다.

##### ObjectPool.cs

{% highlight cs %}
#region Initialize
private void InitVariables()
{
    poolDict = new Dictionary<string, Queue<GameObject>>();
}

private void CreatePools()
{
    foreach (Pool pool in pools)
    {
        Queue<GameObject> objectPool = new Queue<GameObject>();
        for (int i = 0; i < pool.Size; i++)
        {
            int randomIndex = Random.Range(0, pool.Prefabs.Count - 1);
            GameObject obj = Instantiate(pool.Prefabs[randomIndex]);
            obj.SetActive(false);
            objectPool.Enqueue(obj);
        }
        poolDict.Add(pool.Tag.ToString(), objectPool);
    }
}
#endregion
{% endhighlight %}

<kbd>Garbage Pool</kbd>의 생성 과정을 보면 랜덤으로 `prefabs`리스트에서 하나를 가져와 `Instantiate()`하고 딕셔너리 큐에 담는다.

### b. Random Spawner

<figure>
<img src="/project-garbageCollector/spawnArea.gif" alt=" Random Spawner">
 <figcaption>Fig 10. Random Spawner.</figcaption>
</figure>

<kbd>Random Spawner</kbd>를 원하는 곳에 배치하고 범위를 정할 수 있다. 범위는 큐브 형태이다.

##### RandomAreaSpawner.cs

{% highlight cs %}
private void CalculateRandomPoint()
{
    _randomRange = new Vector3(
        Random.Range(-_range.x, _range.x),
        Random.Range(-_range.y, _range.y),
        Random.Range(-_range.z, _range.z));
    _randomPoint = _origin + _randomRange;
}
{% endhighlight %}

`_randomPoint`는 <kbd>Random Spawner</kbd>의 position 값에 `_randomRange` offset을 더한 위치다. `_range`는 `transform.localScale / 2.0f` 값을 가진다.

##### RandomAreaSpawner.cs

{% highlight cs %}
private void RandomPointSpawn()
{
    GameObject obj2Spawn = ObjectPool.Instance.Spawn(spawnObject,
        _randomPoint, Quaternion.identity);
    obj2Spawn.GetComponent<IDependencyInjection>().Injection(gameObject);
}
{% endhighlight %}

<kbd>Random Spawner</kbd>는 <kbd>Garbage Pool</kbd>를 생성할때 자신의 레퍼런스를 담아준다.

### c. Garbage

<figure>
<img src="/project-garbageCollector/garbageDetect.png" alt="Garbage Detect Area">
<figcaption>Fig 11. Garbage Detect Area.</figcaption>
</figure>

Gizmos로 보이는 범위가 <kbd>Garbage</kbd>의 <kbd>Detect Range</kbd>이다. 플레이어가 범위안에 들어오면 **Navmesh Agent**가 활성화 되고 플레이어 반대 방향으로 도망친다.

<figure>
<img src="/project-garbageCollector/navmeshBake.png" alt="Bake Area">
<figcaption>Fig 12. Bake Area.</figcaption>
</figure>

**Bake**된 모습이다. 물 Mesh는 static이 아니기 때문에 static인 plane 하나를 만들어 **Bake**를 하고 Collider와 Mesh Renderer를 꺼버렸다.

<figure>
<img src="/project-garbageCollector/collectGarbage.gif" alt="Collect Garbage">
<figcaption>Fig 13. Collect Garbage.</figcaption>
</figure>

플레이어가 <kbd>Garbage</kbd>를 건져내는 느낌은 이 게임의 핵심이다.

##### GarbageController.cs

{% highlight cs %}
public void Captured()
{
    _isCaptured = true;
    RB.AddForce(Vector3.up * 9000, ForceMode.Impulse);
    Instantiate(impactOnGarbage, transform.position, Quaternion.identity);
}
{% endhighlight %}

건져내는 느낌을 더 살리기 위해 <kbd>Garbage</kbd>가 잡히는 순간 `Vector3.up` 방향으로 튀어 오르게 했다.

## 5. UI

<figure>
<img src="/project-garbageCollector/inGameUI.png" alt="In Game UI">
<figcaption>Fig 14. In Game UI.</figcaption>
</figure>

<kbd>UI</kbd>는 크게 4가지가 있다; <kbd>Main Menu UI</kbd>, <kbd>In Game UI</kbd>, <kbd>Result UI</kbd>, <kbd>Transition UI</kbd>.

### a. Main Menu UI

<figure>
<img src="/project-garbageCollector/startUI.gif" alt="Main Menu UI">
<figcaption>Fig 15. Main Menu UI.</figcaption>
</figure>

간단한 시작과 종료 UI이다. 

### b. In Game UI

<figure>
<img src="/project-garbageCollector/circleUI.gif" alt="Circle UI">
<figcaption>Fig 16. Circle Progress UI.</figcaption>
</figure>

<kbd>Circle Progress UI</kbd>는 원래 생성되는 <kbd>Garbage</kbd>의 수 와 플레이어가 건져낸 <kbd>Garbage</kbd>의 수를 표현하기 위한 UI 였으나 화면을 너무 많이 잡아먹어서 파이널 버전에서는 빠졌다.

<figure>
<img src="/project-garbageCollector/circleH.png" alt="Circle H">
<figcaption>Fig 17. Circle UI Hierarchy.</figcaption>
</figure>

<kbd>Circle Progress UI</kbd>는 **Slider UI**를 두개를 이용해 만들었다. **Slider**의 손잡이는 게이지에 입체감을 주기위해 납짝하게 만들었고 서로의 영역을 침법하지 않게 하기 위해 **Mask**를 썻다.

| <img src="/project-garbageCollector/circleGlow.png" alt="Circle Glow"> | <img src="/project-garbageCollector/circleSprite.png" alt="Circle Sprite"> |

<figure><figcaption>Fig 18. Circle UI Sprites.</figcaption></figure>

UI 글로우 효과는 이미지 두개를 합쳐서 만들었다. 

### a. Resut UI

<figure>
<img src="/project-garbageCollector/endUI.gif" alt="Result UI">
<figcaption>Fig 19. Result UI.</figcaption>
</figure>

<kbd>Result UI</kbd>는 게임이 타임아웃 되면 사용자의 키보드 인풋을 막고 결과 창이 뜬다.

### a. Transition UI

<figure>
<img src="/project-garbageCollector/transition.gif" alt="Transition UI">
<figcaption>Fig 20. Transition UI.</figcaption>
</figure>

<kbd>Transistion UI</kbd>는 직접 그려서 사용하였다. 

##### LevelLoader.cs

{% highlight cs %}
public void LoadNextScene()
{
    transitionUI.SetActive(true);
    StartCoroutine(LoadSceneAsync(
        SceneManager.GetActiveScene().buildIndex + 1));
}

IEnumerator LoadSceneAsync(int scene)
{
    waveAnimator.SetTrigger("transition");
    yield return new WaitForSeconds(transitionTime);
    AsyncOperation operation = SceneManager.LoadSceneAsync(scene);
    while (!operation.isDone)
    {
        float progress = Mathf.Clamp01(operation.progress / .9f);
        yield return null;
    }
}
{% endhighlight %}

새로운 씬이 로드될때 호출된다. 

## 6. Final Result

<figure>
<img src="/project-garbageCollector/gc_final.gif" alt="Final Result">
<figcaption>Fig 21. Final Result.</figcaption>
</figure>