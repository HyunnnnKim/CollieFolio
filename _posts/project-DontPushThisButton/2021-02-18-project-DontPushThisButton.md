---
layout: post
title: Don't Push This Button
date: 2021-02-23 09:29:20 +0700
modified: 2021-02-23 21:49:47 +07:00
categories: Projects
tags: [HalfLight, Project, VR]
description: VR 타임어택 액션 게임
---

<figure>
<img src="/project-DontPushThisButton/projectpic.png" alt="projectPic">
</figure>

**팀명:** Half-Light\
**팀원:** 박근영, 김현우\
**개발 환경:** Unity 2020.1 URP, Visual Studio, GitLab\
**제작 기간:** 2020.05.01 ~ 2020.08.03 (미완성)\
**YouTube:** [Dungeon Breaker Devlogs](https://www.youtube.com/watch?v=zDeuXitzKdA&list=PLqlJmp0RxHq7NrU9c2W1bA_DKvhSt7GYL)
<br />

<hr>

# Index

**1. [<kbd> 프로젝트 소개 </kbd>](#프로젝트-소개)**
- [<kbd> 목표 </kbd>](#목표)
- [<kbd> 팀 소개 및 개발 일정 </kbd>](#팀-소개-및-개발-일정)

**2. [<kbd> 프로젝트 구현 </kbd>](#프로젝트-구현)**
- [<kbd> Animation Based Enemy </kbd>](#1-animation-based-enemy)
- [<kbd> Ragdoll Based Enemy </kbd>](#2-ragdoll-based-enemy)
- [<kbd> Patrol System </kbd>](#3-patrol-system)
- [<kbd> Navigation System </kbd>](#4-navigation-system)
- [<kbd> Interactive Environment </kbd>](#5-interactive-environment)
<br />

<hr>

# 프로젝트 소개

<kbd>Don't Push This Button</kbd>은 **VR 전략 액션 게임**이다. 최대한 빠른 시간 안에 맵 어딘가에 위치한 **버튼**을 찾아 눌르면 스테이지를 클리어하게 된다. 난이도가 올라갈 수록 맵에 존재하는 무기의 사용 횟수가 줄어들고 적군 또한 늘어나기 때문에 제한된 자원을 현명하게 사용하는 것이 중요하다.

### 목표
첫 VR 게임을 개발하면서 몰입의 중요성을 알게 되었다. **몰입 경험**은 사용자가 마치 그 가상공간안에 들어가 있는 듯한 느낌을 주는 것으로 크게 **Sound**, **Visual**, **Interaction** 3가지 요소로 나뉠 수 있다고 생각한다. 그 중 **Interaction** 에 많은 시간을 투자하였다. PC 게임과는 다른 VR의 장점을 극대화하고 사용자 경험의 질을 높이는 데 집중했다.

### 팀 소개 및 개발 일정
##### : 팀 소개

<figure>
<img src="/project-DontPushThisButton/teamHalfLight_rec.jpg" alt="Half-Light">
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

## 1. Animation Based Enemy
<figure>
<img src="/project-DontPushThisButton/animationenemy.png" alt="animationenemy.png">
<figcaption>Fig 1. Animation based Enemy.</figcaption>
</figure>

처음 적을 만들 때는 애니메이션으로 움직임을 주었다. 애니메이션으로 움직이는 적은 동작 하나하나에 디테일을 살릴 수 있다는 장점이 있다. 하지만 플레이어와의 모든 인터렉션을 애니메이션으로 커버하기에는 한계가 있다. 

### a. Animation Controller

| <img src="/project-DontPushThisButton/animationController.png" alt="animationController"> | <img src="/project-DontPushThisButton/blendtree.png" alt="blendtree"> |

<figure><figcaption>Fig 2. Animation Controller.</figcaption></figure>

애니메이션 상태는 <kbd>Idle</kbd>, <kbd>Crawl</kbd>, <kbd>Attack</kbd>, <kbd>Dead</kbd> 4가지가 있다. 그 중 <kbd>Idle</kbd>과 <kbd>Crawl</kbd>는 **BlendTree**를 이용해 자연스럽게 연결했다.

### b. Structure

#### : class diagram

@startmermaid
classDiagram
    Move --|> IAction
    Combat --|> IAction
    EnemyController --> Move
    EnemyController --> Combat
    Move --> ActionScheduler
    Combat --> ActionScheduler

    <<interface>> IAction
    class IAction {
        +Cancel()
    }
    class ActionScheduler {
        -IAction currentAction
        +StartAction(IAction)
        +CancelCurrentAction()
    }
    class Move {
        +MoveAction(Vector3)
        +Cancel()
    }
    class Combat {
        +Attack(GameObject)
        +Cancel()
    }
    class EnemyController {
        -GameObject _player
        +AttackBehaviour()
        +MoveBehaviour()
    }
@endmermaid

클래스 다이어그램을 보면 적은 <kbd>Move</kbd>와 <kbd>Combat</kbd>이라는 action 클래스를 가지고 있고 둘 다 <kbd>IAction</kbd>이라는 인터페이스의 `Cancel()` 함수를 재정의한다. 재정의된 `Cancel()`은 새로운 action이 실행될 때 <kbd>ActionScheduler</kbd>가 현재 action의 `Cancel()`을 호출한다.

### c. Move

적의 이동은 **Root Motion** 과 **Navmesh Agent**를 같이 사용하였다. **Root Motion**을 사용하면 적의 움직임을 애니메이션이 제어하기 때문에 **Navmesh Agent**의 `.nextPosition`을 업데이트 해줘야 된다.

##### Move.cs

{% highlight cs %}
void OnAnimatorMove()
{
    Vector3 position = animator.rootPosition;
    position.y = navMeshAgent.nextPosition.y;
    transform.position = position;
}
{% endhighlight %}

OnAnimatorMove() 콜백함수에서 포지션 y를 업데이트한다.

{% highlight cs %}
void OnAnimatorMove()
private void RootMotionFollow()
{
    Vector3 worldDeltaPosition = navMeshAgent.nextPosition
        - transform.position;
    if (worldDeltaPosition.magnitude > navMeshAgent.radius)
    {
        navMeshAgent.nextPosition = transform.position
            + 0.9f * worldDeltaPosition;
    }
}
{% endhighlight %}

### d. Attack

적은 주먹을 날리는 애니메이션으로 플레이어를 공격한다. 플레이어가 체력이 깍이는 타이밍을 맞추기 위해 주먹을 날리는 애니메이션에 **이벤트 함수**를 심어 넣었다.

##### Combat.cs

{% highlight cs %}
void OnAnimatorMove()
private void TriggerAttack()
{
    /* Face the target direction */
    transform.LookAt(target.transform.position);
    /* Attack only when time since last attack
    is greater than given time. */
    if (timeSinceLastAttack > timeBetweenAtacts)
    {
        animator.ResetTrigger("cancel");
        /* This will trigger the OnAnimatorHit() event. */
        animator.SetTrigger("attack");
        timeSinceLastAttack = 0;
    }
}
{% endhighlight %}

{% highlight cs %}
void AnimationHit()
{
    if (target == null) return;
    target.TakeDamage(damage);
}
{% endhighlight %}

## 2. Ragdoll Based Enemy
<figure>
<img src="/project-DontPushThisButton/skellieNight.png" alt="skellieNight.png">
<figcaption>Fig 3. Ragdoll based Enemy.</figcaption>
</figure>

애니메이션을 기반으로 한 적은 물리 인터렉션이 불가능했다. VR에서는 특히 플레이어가 손을 움직이고 만질 수 있어서 애니메이션으로 미리 짜인 움직임만 하는 적은 몰입도를 떨어트렸다.

반면에 **Active Ragdoll**은 물리 인터렉션이 가능해서 플레이어가 적을 밀치거나 넘어트리는 게 가능했고 더 자연스러운 인터렉션이 몰입도를 높일 수 있었다.

### a. Structure

기본적인 **Active Ragdoll**의 상태는 4가지가 있다; <kbd>FellDown</kbd>, <kbd>OutOfBalance</kbd>, <kbd>InBalance</kbd>, <kbd>InAir</kbd>. 여기에 추가적으로 **Enemy**의 상태 4가지가 추가된다; <kbd>Dead</kbd>, <kbd>Combat</kbd>, <kbd>Guard</kbd>, <kbd>Idle</kbd>.

### b. Self Balance
<figure>
<img src="/project-DontPushThisButton/selfbalance.gif" alt="selfbalance.gif">
<figcaption>Fig 4. Active Ragdoll Self Balance.</figcaption>
</figure>

**YouTube Link :** [Devlog #2](https://www.youtube.com/watch?v=2zuM4VvnpB4)

<kbd>Self Balance</kbd>는 hip에 있는 **Configurable Joint**에 의해 많은 부분 해결된다. Joint의 Angular Drive를 강하게 하고 Z축 로테이션을 잠그면 Ragdoll은 넘어지지 않는다. 

하지만 플레이어 재미를 위해 Ragdoll은 넘어져야 한다. 자연스럽게 넘어지려면 Ragdoll의 **Center of Mass(CoM)**이 **양발의 중심점**에서 벗어나기 시작하면 다리를 움직여 균형을 잡게 하고 많이 벗어났을때 Ragdoll의 모든 Joint의 힘을 풀어주면 넘어지게 된다.

<figure>
<img src="/project-DontPushThisButton/gait.png" alt="gait.png">
<figcaption>Fig 5. Active Ragdoll Gait Cycle.</figcaption>
</figure>

##### ActiveRagdoll.cs

{% highlight cs %}
private void CalculateCoM()
{
    Vector3 com = Vector3.zero; /* Center of mass */
    float som = 0; /* Sum of mass. */

    if (setup.RigidbodyNodes != null)
    {
        foreach (Rigidbody childNode in setup.RigidbodyNodes)
        {
            com += childNode.worldCenterOfMass * childNode.mass;
            som += childNode.mass;
        }
        com /= som;
    }
    CoM.transform.position = com;
}
{% endhighlight %}

균형을 잡기위한 다리의 움직임은 넘어지는 방향에 따라 달라진다.

##### BalanceMovement.cs

{% highlight cs %}
if (ragdoll.LeanState.Equals(LeanState.Right))
{
    legBase.ActivateRightLeg();
    legBase.PreviousLeg = ActiveLeg.LeftLeg;
    legBase.BackwardMovementFootMaterialChange(setup);

    legBase.LegJointsMovement(setup, balanceRot.SideUpperStance, balanceRot.SideLowerStance, balanceRot.SideAnkleStance,
        balanceRot.SideUpperSwing_S, balanceRot.SideLowerSwing_S, balanceRot.SideAnkleSwing_S,
        balanceRot.SideUpperSwing_F, balanceRot.SideLowerSwing_F, balanceRot.SideAnkleSwing_F, 0.2f);

    spineBase.CoreJointMovement(setup, balanceRot.SideSpine, balanceRot.SideSpine, 0.5f);
}
{% endhighlight %}

움직이는 다리의 발은 **PhysicsMaterial**이 미끄러운 값으로 바뀐다. 움직일때 발이 걸려 넘어지는 문제를 막아준다. 다리의 움직임은 줄때는 Joint의 **TargetRotation** 값을 타겟 값까지 Lerp한다. 

### c. Get Up
<figure>
<img src="/project-DontPushThisButton/standup.gif" alt="standup.gif">
<figcaption>Fig 6. Active Ragdoll Get Up.</figcaption>
</figure>

**YouTube Link :** [Devlog #3](https://www.youtube.com/watch?v=Yy6NPGpwW7Q&t=83s)

Ragdoll은 넘어지고 다시 일어날때 Joint의 힘을 다시 주고 **AngularXZMotion**에 락을 줘서 hip의 균형을 잡는다.

##### GetUpMovement.cs

{% highlight cs %}
legBase.BackwardMovementFootMaterialChange(setup);
jointPower.StrengthenAllJoints(setup);
jointPower.LockAngularXZMotion(setup);

legBase.LegJointsMovement(setup, getUpRot.Upper_S, getUpRot.Lower_S, getUpRot.Ankle_S,
    getUpRot.Upper_S, getUpRot.Lower_S, getUpRot.Ankle_S,
    getUpRot.Upper_F, getUpRot.Lower_F, getUpRot.Ankle_F, lerpTime);

spineBase.CoreJointMovement(setup, getUpRot.Spine_S, getUpRot.Spine_F, lerpTime);

if (!ragdoll.ARState.Equals(ARState.FellDown)) { delayExecuted = true; }
{% endhighlight %}

### d. Attack
<figure>
<img src="/project-DontPushThisButton/ragdollstab.gif" alt="ragdollstab.gif">
</figure>

| <img src="/project-DontPushThisButton/beanthrow.gif" alt="beanthrow.gif"> | <img src="/project-DontPushThisButton/ragdollpunch.gif" alt="ragdollpunch.gif"> |

<figure><figcaption>Fig 7. Active Ragdoll Attacks.</figcaption></figure>

**YouTube Link :** [Devlog #4](https://www.youtube.com/watch?v=Qa51O93OS5w&t=135s)

무기를 이용한 공격 클래스는 **ICombat**의 `Attack()` 함수를 재정의한다. 

##### Stab.cs

{% highlight cs %}
public void Attack(ActiveRagdollSetup setup, Vector3 targetPosition)
{
    jointPower.PowerfulArm(setup, activeArm);

    setup.cJoints.Spine1.targetRotation = combatRot.StabSpineOne_S * startRot.Spine1;
    setup.cJoints.Spine2.targetRotation = combatRot.StabSpineTwo_S * startRot.Spine2;

    ActiveRagdollSetup.SideJoints armJoints = activeArm.Equals(ActiveArm.Right) ? setup.rJoints : setup.lJoints;

    armJoints.Shoulder.targetRotation = combatRot.StabShoulder_S * startRot.Shoulder;
    armJoints.Elbow.targetRotation = combatRot.StabElbow_S * startRot.Elbow;

    StartCoroutine(stabbing(setup, armJoints, targetPosition));
}
{% endhighlight %}

`stabbing()` 동작이 일어나기전 준비 자세를 취한다.

### e. Look At

<figure>
<img src="/project-DontPushThisButton/lookat.gif" alt="lookat.gif">
<figcaption>Fig 8. View Angle.</figcaption>
</figure>

Ragdoll은 **View Angle**이 있다. 플레이어가 시야에 들어와야지만 플레이어를 쫓기 시작한다.

##### ActiveRagdoll.cs

{% highlight cs %}
private bool PlayerInViewAngle()
{
    float cosAngle = Vector3.Dot(headToTargetDir,
        setup.cJoints.Head.transform.forward);
    float angle = Mathf.Acos(cosAngle) * Mathf.Rad2Deg;

    return angle < AngleCutOff;
}
{% endhighlight %}

### f. Interactions

<figure>
<img src="/project-DontPushThisButton/push.gif" alt="push.gif">
<figcaption>Fig 9. Push.</figcaption>
</figure>

<figure>
<img src="/project-DontPushThisButton/ragdollhandshake.gif" alt="ragdollhandshake.gif">
<figcaption>Fig 10. Touch.</figcaption>
</figure>

<figure>
<img src="/project-DontPushThisButton/ragdollhomerun.gif" alt="ragdollhomerun.gif">
<figcaption>Fig 11. Home Run.</figcaption>
</figure>

<figure>
<img src="/project-DontPushThisButton/explode.gif" alt="explode.gif">
<figcaption>Fig 12. Explosion.</figcaption>
</figure>

<figure>
<img src="/project-DontPushThisButton/hitreact.gif" alt="hitreact.gif">
<figcaption>Fig 13. Gun Shot.</figcaption>
</figure>

<figure>
<img src="/project-DontPushThisButton/run.gif" alt="run.gif">
<figcaption>Fig 14. Overload.</figcaption>
</figure>


## 3. Patrol System

![](https://www.youtube.com/watch?v=0Vs6KuCM2oQ)

point를 이용한 간단한 **Patrol System**이다. 빈오브젝트로 point를 배치하고 인스펙터에서 모드를 선택할 수 있다.

### a. Linear

##### PathController.cs

{% highlight cs %}
if (SelectedPath.Equals(PathType.Linear))
{
    for (int i = 0; i < transform.childCount; i++)
    {
        if (i == transform.childCount - 1)
        {
            Gizmos.DrawSphere(GetGuardPoint(i), 0.1f);
            break;
        }
        Gizmos.DrawSphere(GetGuardPoint(i), 0.1f);
        Gizmos.DrawLine(GetGuardPoint(i), GetGuardPoint(i + 1));
    }
}
{% endhighlight %}

{% highlight cs %}
public int GetNextLinearIndex(int i)
{
    if (i + 1 == transform.childCount) { linearIndexAdder = -1; }
    if (i == 0) { linearIndexAdder = 1; }
    return i + linearIndexAdder;
}
{% endhighlight %}

### b. Loop

##### PathController.cs

{% highlight cs %}
Gizmos.color = Color.cyan;
if (SelectedPath.Equals(PathType.Loop))
{
    for (int i = 0; i < transform.childCount; i++)
    {
        int j = GetNextLoopIndex(i);
        Gizmos.DrawSphere(GetGuardPoint(i), 0.1f);
        Gizmos.DrawLine(GetGuardPoint(i), GetGuardPoint(j));
    }
}
{% endhighlight %}

{% highlight cs %}
public int GetNextLoopIndex(int i)
{
    if (i + 1 == transform.childCount) { return 0; }
    return i + 1;
}
{% endhighlight %}

## 4. Navigation System

![](https://www.youtube.com/watch?v=EblRS7sLzEg)

### a. Agent

Ragdoll에 **Navmesh Agent**를 직접 적용할 수 없으므로 Ragdoll이 Agent를 따라가고 Agent가 타겟을 따라가게 한다. 이때 Agent는 일정 거리까지만 움직이게 속도를 제한한다. 

##### PathController.cs

{% highlight cs %}
private void MoveAgent(Vector3 destination)
{
    navMeshAgent.destination = destination;
    navMeshAgent.isStopped = false;

    var dst = (navMeshAgent.transform.position
        - ragdoll.CoM.transform.position).sqrMagnitude;
    navMeshAgent.speed = ((maxDistance - dst) / maxDistance) * maxSpeed;
}
{% endhighlight %}

## 5. Interactive Environment

<figure>
<img src="/project-DontPushThisButton/dungeon.gif" alt="dungeon.gif">
<figcaption>Fig 15. Dungeon.</figcaption>
</figure>

### a. Gate

<figure>
<img src="/project-DontPushThisButton/gate.gif" alt="gate.gif">
<figcaption>Fig 16. Gate.</figcaption>
</figure>

### b. Coffin

<figure>
<img src="/project-DontPushThisButton/coffin.gif" alt="coffin.gif">
<figcaption>Fig 17. Coffin.</figcaption>
</figure>

### c. Bridge

<figure>
<img src="/project-DontPushThisButton/bridge.gif" alt="bridge.gif">
<figcaption>Fig 18. Bridge.</figcaption>
</figure>