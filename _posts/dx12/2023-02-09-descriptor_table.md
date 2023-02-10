---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Descriptor Table"
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

# 🔷 Descriptor Table

## 1. Root Signature 변경

```cpp
// b0, b1, b2, b3, b4
CD3DX12_DESCRIPTOR_RANGE range[] =
{
    CD3DX12_DESCRIPTOR_RANGE(D3D12_DESCRIPTOR_RANGE_TYPE_CBV, CBV_REGISTER_COUNT, 0)
};

CD3DX12_ROOT_PARAMETER param[1];
param[0].InitAsDescriptorTable(_countof(range), range);
```

---

<br>

## 2. TableDescriptorHeap Class

```cpp
#pragma once
class TableDescriptorHeap
{
public:
	void Init(uint32 count);

public:
	void Clear();

	void SetConstantBufferView(D3D12_CPU_DESCRIPTOR_HANDLE srcHandle, CBV_REGISTER reg);
	void CommitTable();

	ComPtr<ID3D12DescriptorHeap> GetDescriptorHeap() { return _descHeap; }
	D3D12_CPU_DESCRIPTOR_HANDLE GetCPUHandle(CBV_REGISTER reg);
private:
	D3D12_CPU_DESCRIPTOR_HANDLE GetCPUHandle(uint32 reg);

private:
	ComPtr<ID3D12DescriptorHeap>	_descHeap;
	uint64							_handleSize = 0;
	uint64							_groupSize = 0;
	uint64							_groupCount = 0;

	uint32							_currentGroupIndex;
};
```

```cpp
#include "pch.h"
#include "TableDescriptorHeap.h"
#include "Engine.h"
#include "Device.h"
#include "CommandQueue.h"

void TableDescriptorHeap::Init(uint32 count)
{
	_groupCount = count;

	D3D12_DESCRIPTOR_HEAP_DESC desc = {};
	desc.NumDescriptors = count * REGISTER_COUNT;
	desc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE;
	desc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;

	MyEngine->GetDevice()->GetDevice()->CreateDescriptorHeap(&desc, IID_PPV_ARGS(&_descHeap));

	_handleSize = MyEngine->GetDevice()->GetDevice()->GetDescriptorHandleIncrementSize(desc.Type);
	_groupSize = _handleSize * REGISTER_COUNT;
}

void TableDescriptorHeap::Clear()
{
	_currentGroupIndex = 0;
}

void TableDescriptorHeap::SetConstantBufferView(D3D12_CPU_DESCRIPTOR_HANDLE srcHandle, CBV_REGISTER reg)
{
	D3D12_CPU_DESCRIPTOR_HANDLE handle = GetCPUHandle(reg);

	uint32 descRange = 1;
	uint32 srcRange = 1;
	MyEngine->GetDevice()->GetDevice()->CopyDescriptors(1, &handle, &descRange, 1, &srcHandle, &srcRange, D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV);
}

void TableDescriptorHeap::CommitTable()
{
	D3D12_GPU_DESCRIPTOR_HANDLE handle = _descHeap->GetGPUDescriptorHandleForHeapStart();
	handle.ptr += _currentGroupIndex * _groupSize;
	MyEngine->GetCommandQueue()->GetCommandList()->SetGraphicsRootDescriptorTable(0, handle);

	_currentGroupIndex++;
}

D3D12_CPU_DESCRIPTOR_HANDLE TableDescriptorHeap::GetCPUHandle(CBV_REGISTER reg)
{
	return GetCPUHandle(static_cast<uint32>(reg));
}

D3D12_CPU_DESCRIPTOR_HANDLE TableDescriptorHeap::GetCPUHandle(uint32 reg)
{
	D3D12_CPU_DESCRIPTOR_HANDLE handle = _descHeap->GetCPUDescriptorHandleForHeapStart();
	handle.ptr += _currentGroupIndex * _groupSize;
	handle.ptr += reg * _handleSize;
	return handle;
}
```

<br>

## 3. ConstantBufferView 생성

```cpp
	ComPtr<ID3D12DescriptorHeap>	_cbvHeap;
	D3D12_CPU_DESCRIPTOR_HANDLE		_cpuHandleBegin = {};
	uint32							_handleIncrementSize = 0;
```

```cpp
D3D12_CPU_DESCRIPTOR_HANDLE ConstantBuffer::PushData(int32 rootParamIndex, void* buffer, uint32 size)
{
	assert(_currentIndex < _elementSize);

	::memcpy(&_mappedBuffer[_currentIndex * _elementSize], buffer, size);

	//D3D12_GPU_VIRTUAL_ADDRESS address = GetGpuVirtualAddress(_currentIndex);
	//MyEngine->GetCommandQueue()->GetCommandList()->SetGraphicsRootConstantBufferView(rootParamIndex, address);

    D3D12_CPU_DESCRIPTOR_HANDLE cpuHandle = GetCpuHandle(_currentIndex++);
	return cpuHandle;
}

void ConstantBuffer::CreateView()
{
	D3D12_DESCRIPTOR_HEAP_DESC desc = {};
	desc.NumDescriptors = _elementCount;
	desc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	desc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
	MyEngine->GetDevice()->GetDevice()->CreateDescriptorHeap(&desc, IID_PPV_ARGS(&_cbvHeap));

	_cpuHandleBegin = _cbvHeap->GetCPUDescriptorHandleForHeapStart();
	_handleIncrementSize = MyEngine->GetDevice()->GetDevice()->GetDescriptorHandleIncrementSize(desc.Type);

	for (uint32 i = 0; i < _elementCount; ++i)
	{
		D3D12_CPU_DESCRIPTOR_HANDLE handle = GetCPUHandle(i);

		D3D12_CONSTANT_BUFFER_VIEW_DESC desc = {};
		desc.BufferLocation = _cbvBuffer->GetGPUVirtualAddress() + static_cast<uint64>(_elementSize) * i;
		desc.SizeInBytes = _elementSize;

		MyEngine->GetDevice()->GetDevice()->CreateConstantBufferView(&desc, handle);
	}
}
```

## 4. CommandQueue 설정

```cpp
// Render Begine 설정
MyEngine->GetTableDescriptorHeap()->Clear();

// TableDescriptorHeap
ID3D12DescriptorHeap* descHeap = MyEngine->GetTableDescriptorHeap()->GetDescriptorHeap().Get();
_cmdList->SetDescriptorHeaps(1, &descHeap);
```

<br>

## 5. Mesh에서 값 설정
```cpp
// 1) Buffer에다가 데이터 세팅
// 2) TableDescHeap에다가 CBV 전달
// 3) 모두 세팅이 끝났으면 TableDescHeap 커밋
{
    D3D12_CPU_DESCRIPTOR_HANDLE handle = MyEngine->GetConstantBuffer()->PushData(0, &_transform, sizeof(_transform));
    MyEngine->GetTableDescriptorHeap()->SetConstantBufferView(handle, CBV_REGISTER::b0);
}
{
    D3D12_CPU_DESCRIPTOR_HANDLE handle = MyEngine->GetConstantBuffer()->PushData(0, &_transform, sizeof(_transform));
    MyEngine->GetTableDescriptorHeap()->SetConstantBufferView(handle, CBV_REGISTER::b1);
}
MyEngine->GetTableDescriptorHeap()->CommitTable();
```

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)