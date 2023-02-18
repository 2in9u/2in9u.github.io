---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Component"
categories:
    - dx12
# tag:
#     [cpp, c++, oop]
toc_label: "목차"
toc: true
toc_sticky: true
date: 2023-02-17
last_modified_at: 2023-02-17

#published: false
---

# 🔷 Component 구조
- 독립된 모듈(component)을 조립하여 하나의 객체를 만듦  
- 재사용성⬆️, 활용성⬆️  

---

## 1. Component Class
- Component의 최상위 클래스 

```cpp
enum class COMPONENT_TYPE : uint8
{
	TRANSFORM,
	MESH_RENDERER,

	SCRIPT,
	END
};

enum
{
	FIXED_COMPONENT_COUNT = static_cast<uint8>(COMPONENT_TYPE::END) - 1,
};

class Component
{
public:
	Component(COMPONENT_TYPE type);
	virtual ~Component();

public:
	// GameObject의 흐름에 따라 Component를 갱신하기 위한 함수
	// 파생클래스에서 구현
	virtual void Awake() abstract;
	virtual void Start() abstract;
	virtual void Update() abstract;
	virtual void LateUpdate() abstract;

public:
	shared_ptr<GameObject> GetGameObject() { return _gameObject.lock(); }

	bool IsValid() { return _gameObject.expired() == false; }
	COMPONENT_TYPE GetType() { return _type; }

private:
	COMPONENT_TYPE			_type;
	weak_ptr<GameObject>	_gameObject; // 현재 컴포넌트를 소유하는 GameObject
};
```

---

<br>

### 1.1. Transform Class
- Transform를 관리하는 컴포넌트 클래스  

```cpp
struct TransformMatrix
{
	Vec4 offset;
};


class Transform : public Component 
{ 
	... 
};
```

### 1.2. Mesh_Renderer Class
- Mesh를 렌더링하지 위한 컴포넌트 클래스  

```cpp
class MeshRenderer : public Component
{
	...
private:
	// [📂. Material]에서 mesh에 material을 넣어서 관리하는 부분을 분리시키고 MeshRenderer클래스에서 각각을 관리한다.
	shared_ptr<Mesh>		_mesh;
	shared_ptr<Material>	_material;
};
```

### 1.3. Script Class
- 스크립트를 관리하는 컴포넌트 클래스  
- 다른 컴포넌트 클래스와 다르게 1개 이상을 가질 수 있다.  

```cpp
class Script : public Component 
{
	...
};
```

<br>

## 2. GameObject Class
- 물체 클래스  
- 컴포넌트들을 가진다.  

> [enable_shared_from_this](https://learn.microsoft.com/ko-kr/cpp/standard-library/enable-shared-from-this-class?view=msvc-170)
>---

```cpp
class GameObject : public enable_shared_from_this<GameObject>
{
	...

public:
	// GameObject가 `실행되는 흐름`을 정하여 함수를 실행  
	void Awake();		// component들이 Awake를 실행
	void Start(); 		// component들이 Start를 실행
	void Update();		// component들이 Update를 실행
	void LateUpdate();	// component들이 LateUpdate를 실행

	void AddComponent(shared_ptr<Component> component);

private:
	array<shared_ptr<Component>, FIXED_COMPONENT_COUNT>		_components;	// 가지고 있는 모듈
	vector<shared_ptr<Script>>								_scripts;		// 스크립트 모듈
};
```

> - 개체가 enable_shared_from_this 기본 클래스에서 파생될 경우 shared_from_this 템플릿 멤버 함수는 이 인스턴스의 소유권을 기존 shared_ptr 소유자와 공유하는 shared_ptr 클래스 개체를 반환합니다. 그렇지 않으면 this에서 새 shared_ptr를 만들 경우 기존 shared_ptr 소유자와 완전히 다르므로 잘못된 참조가 발생하거나 개체가 두 번 이상 삭제될 수 있습니다. 개체가 아직 소유 shared_ptr 하지 않은 인스턴스를 호출 shared_from_this 하는 경우 동작이 정의되지 않습니다.

<br>


# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)