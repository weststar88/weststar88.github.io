---
layout: post
title:  "Unity Transform"
categories: Study
tags: [Unity,Game]
---

unityTips 알아두면 쓸데있는 트랜스폼

![Unity](/assets/images/Unity.png)

- 유니티는 사용자가 접근하는 C#코드, 엔진내부에서 돌아가는 C++, 물리엔진은 2D용 물리엔진(Box2D)과 3D(Physx)용 물리엔진으로 구성되어있음
	rigidbody2D, boxCollider2D 가 Box2D엔진을 사용하는 컴포넌트라고 보면 됨
- 우리가 C#으로 Translate 함수를 호출하게 되면 결과적으로 c++함수인 TransformDirection 함수를 호출하게 되어있음.(유니티 github쪽에 보면 C# api는 공개가 되어있다.)
- 게임오브젝트를 하나 만든다고 하면 C#공간, C++공간, 물리공간이 존재하게 된다.
	* 사용자가 C# API로 접근해서 transform을 조정하면 변경된 정보를 C++, physics쪽에도 같이 정보를 공유해주고
	* animator에서 transform을 조정하면 (엔진쪽에서 발생하는것같음) C#, physics쪽에 공유하게 되고
	* rigidbody.addForce와 같이 물리쪽에서 조정을 해도 C#, C++쪽에 공유하게 되고
	* 결국 이 세가지 모두가 항상 같이 상호작용을 하게 된다.

- position, rotation, scale이 변수가 따로따로 저장되있는거라 생각할 수 있겠지만
	하나의 4x4 matrix형태로 저장이 되어있다. 그래서 그냥 position만 바꾼다고해도 서로 다같이(position, rotation, scale) 영향을 미친다.
	수포자를 위한 게임수락 #17 행렬과 트랜스폼 을 찾아보면 자세히 설명이 나와있다.

{% highlight c# %}
public void Update()
{
	transform.position += new = Vector3(1f,1f,1f);
	transform.rotation += Quaternion.Eular(45f,45f,45f);
}
{% endhighlight %}
그래서 이런식으로 코드를 짜면 사실 쓸데없이 두번 연산을 하게 되는일이 발생한다.

{% highlight c# %}
public void Update()
{
	transform.SetPositionAndRotation(
		transform.position + new Vector3(1f,1f,1f),
		transform.rotation + Quaternion.Eular(45f,45f,45f)
	);
	transform.position += new = Vector3(1f,1f,1f);
	transform.rotation += Quaternion.Eular(45f,45f,45f);
}
{% endhighlight %}
이렇게 사용하면 연산을 한번만 할 수 있게된다. (추천)

많은 시스템에서 트랜스폼을 업데이트 하고있다면 (Jump.cs, Run.cs, Dash.cs 등등) 
모든 변경사항을 모아서 한군데서 처리하면 연산을 많이 줄일 수 있을것이다.

{% highlight c# %}
[RequireComponent(typeof(RigidBody))]
class MyMono : MonoBehaviour
{
	void Update()
	{
		transform.SetPositionAndRotation(...);
	}
}
{% endhighlight %}
이런식으로 rigidbody가 같이 붙게되는(붙어있는?) 오브젝트의 transform을 건드리게 되면
처음에 말했던 c#, c##, physics가 상호작용한다는 부분때문에
서로가 서로에게 영향을 미쳐서 (코드는 position을 바꾸고있고, rigidbody가 붙어있는 컴포넌트에서는 따로 물리연산을 실행함으로써) 연산 낭비가 일어나게 된다.

이런걸 알고있음에도 게임을 구현하다보면 어쩔수없이 이렇게 사용할수밖에 없는 경우가 생긴다.
그래서 유니티 프로젝트세팅-physics 쪽에 들어가보면 Auto Sync Transforms 라는 옵션이 존재하는데
이걸 조절하여 transform을 바꿀때마다 항상 물리쪽 싱크를 맞춰줄거냐 아니면 물리업데이트 주기에 맞춰서 해줄거냐 설정할 수 있다. (최적화옵션)

상위오브젝트의 transform을 조절하게 되면 하위에있는 모든 오브젝트의 transform에 영향을 미치기 때문에 주의해서 조절하는게 좋다.
캐릭터 같은경우 어쩔수없이 계층구조가 생길수밖에없는데, 이때 캐릭터 import 세팅쪽을 보면 optimize Game Object 라는 옵션을 켜주게 되면

![optimize Game Object](/assets/images/optimize Game Object.png)

유니티 하이어라키쪽에서 생략해서 보여주게됨
그럼 사용자 입장에서 더 이상 필요없는 하위구조에 접근할수없게 되니까 성능을 좀 더 높힐 수 있다.
