---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Constant Buffer"
categories:
    - dx12
# tag:
#     [cpp, c++, oop]
toc_label: "목차"
toc: true
toc_sticky: true
date: 2023-02-09
last_modified_at: 2023-02-09

#published: false
---

# 🔷 Constant Buffer

## 1. Root Signature의 파라미터에 상수 버퍼 생성

```cpp
// (1) 상수버퍼 생성
CD3DX12_ROOT_PARAMETER param[2];
param[0].InitAsConstantBufferView(0); // b0 (CBV)
param[1].InitAsConstantBufferView(1); // b1
// (2) 파라미터 정보 설정
D3D12_ROOT_SIGNATURE_DESC desc = CD3DX12_ROOT_SIGNATURE_DESC(2, param);
```

<br>

## 2. ConstantBuffer Class
- 상수 버퍼를 관리하는 클래스  

> `상수 버퍼(constant buffer)`
> - 셰이더 프로그램에서 참조하는 상수 자료를 담는 GPU자원  
> - CPU가 프레임 마다 한 번씩 갱신하는 것이 일반적  
> - 버퍼의 크기는 최소하드웨어 할당 크기(256byte)의 배수  

---

```cpp
ComPtr<ID3D12Resource>	_cbvBuffer; // 상수 버퍼
BYTE*					_mappedBuffer = nullptr;
uint32					_elementSize = 0;
uint32					_elementCount = 0;

uint32					_currentIndex = 0;
```

1) 상수버퍼 크기 및 개수 설정

```cpp
_elementSize = (size + 255) & ~255;	// 크기 (256byte의 배수)
_elementCount = count;				// 버퍼 개수
```

2) 상수버퍼 생성

```cpp
uint32 bufferSize = _elementSize * _elementCount;
D3D12_HEAP_PROPERTIES heapProperty = CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD); // 업로드 힙
D3D12_RESOURCE_DESC desc = CD3DX12_RESOURCE_DESC::Buffer(bufferSize);

// 상수버퍼 생성
MyEngine->GetDevice()->GetDevice()->CreateCommittedResource(
	&heapProperty,
	D3D12_HEAP_FLAG_NONE,
	&desc,
	D3D12_RESOURCE_STATE_GENERIC_READ,
	nullptr,
	IID_PPV_ARGS(&_cbvBuffer));
```

3) 상수버퍼 값 설정

```cpp
// (1) 자료를 올리기 위한 상수버퍼를 가리키는 포인터 얻기
_cbvBuffer->Map(0, nullptr, reinterpret_cast<void**>(&_mappedBuffer));
assert(_currentIndex < _elementSize);
// (2) CPU(시스템메모리)에서 자료를 상수버퍼에 복사
::memcpy(&_mappedBuffer[_currentIndex * _elementSize], buffer, size);
```

4) 파이프라인에 상수버퍼 바인딩
```cpp
D3D12_GPU_VIRTUAL_ADDRESS address = GetGpuVirtualAddress(_currentIndex);
MyEngine->GetCommandQueue()->GetCommandList()->SetGraphicsRootConstantBufferView(rootParamIndex, address);
_currentIndex++;
```

> `상수버퍼 포인터 얻기`
>
> ```cpp
>	D3D12_GPU_VIRTUAL_ADDRESS objCBAddress = _cbvBuffer->GetGPUVirtualAddress(); // GPU상의 시작주소를 알려준다
>	objCBAddress += index * _elementSize;
>	return objCBAddress;
> ```

> `Mesh에서 값 설정`
>```cpp
>MyEngine->GetConstantBuffer()->PushData(0, &_transform, sizeof(_transform)); // 좌표 (b0)
>MyEngine->GetConstantBuffer()->PushData(1, &_transform, sizeof(_transform)); // 색 (b1)
>```

<br>

## 3. HLSL에 상수버퍼 자료 추가

1) 상수버퍼 
- register `b`를 사용한다  

```cpp
cbuffer TEST_b0 : register(b0)
{
    float4 offset0;
}

cbuffer TEST_b1 : register(b1)
{
    float4 offset1;
}
```

```cpp
VS_OUT VS_Main(VS_IN input)
{
    VS_OUT output = (VS_OUT)0;

    output.pos = float4(input.pos, 1.f);
    output.pos += offset0; // pos에 상수버퍼 값 더하기
    output.color = input.color;
    output.color += offset1; // color에 상수버퍼 값 더하기

    return output;
}
```

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)