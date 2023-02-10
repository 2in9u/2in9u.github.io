---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Texture Mapping"
categories:
    - dx12
# tag:
#     [cpp, c++, oop]
toc_label: "목차"
toc: true
toc_sticky: true
date: 2023-02-10
last_modified_at: 2023-02-10

#published: false
---

# 🔷 Texture Mapping

## 🔹 [DirectXTex](https://github.com/microsoft/DirectXTex) 설정
- `#include <d3d12.h>` 전에 `#include "DirectXTex.h"`필요
[DirectXTex 시작 매뉴얼](https://github.com/microsoft/DirectXTex/wiki/DirectXTex)

<br>

## 1. Texture Class

```cpp
#pragma once
class Texture
{
public:
	void Init(const wstring& path);

public:
	void CreateTexture(const wstring& path);
	void CreateView();

	D3D12_CPU_DESCRIPTOR_HANDLE GetCpuHandle() { return _srvHandle; }

private:
	ScratchImage					_image;	// 이미지 또는 이미지 집합을 반환하는 함수의 메모리를 관리하는 도우미 클래스
	ComPtr<ID3D12Resource>			_tex2D;

	ComPtr<ID3D12DescriptorHeap>	_srvHeap;
	D3D12_CPU_DESCRIPTOR_HANDLE		_srvHandle;
};
```

```cpp
#include "pch.h"
#include "Texture.h"
#include "Engine.h"
#include "Device.h"
#include "CommandQueue.h"
#include <filesystem>

void Texture::Init(const wstring& path)
{
	CreateTexture(path);
	CreateView();
}

void Texture::CreateTexture(const wstring& path)
{
	wstring ext = std::filesystem::path(path).extension();

	if (ext == L".dds" || ext == L".DDS")
		::LoadFromDDSFile(path.c_str(), DDS_FLAGS_NONE, nullptr, _image);
	else if (ext == L".tga" || ext == L".TGA")
		::LoadFromTGAFile(path.c_str(), nullptr, _image);
	else // png, jpg, jpeg, bmp
		::LoadFromWICFile(path.c_str(), WIC_FLAGS_NONE, nullptr, _image);

	ComPtr<ID3D12Device> device = MyEngine->GetDevice()->GetDevice();
	HRESULT hr = ::CreateTexture(device.Get(), _image.GetMetadata(), &_tex2D);
	assert(SUCCEEDED(hr));

	vector<D3D12_SUBRESOURCE_DATA> subResources;

	hr = ::PrepareUpload(
		device.Get(),
		_image.GetImages(),
		_image.GetImageCount(),
		_image.GetMetadata(),
		subResources);

	assert(SUCCEEDED(hr));

	const uint64 bufferSize = ::GetRequiredIntermediateSize(_tex2D.Get(), 0, static_cast<uint32>(subResources.size()));

	D3D12_HEAP_PROPERTIES heapProperty = CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD);
	D3D12_RESOURCE_DESC desc = CD3DX12_RESOURCE_DESC::Buffer(bufferSize);

	ComPtr<ID3D12Resource> textureUploadHeap;
	hr = device->CreateCommittedResource(
		&heapProperty,
		D3D12_HEAP_FLAG_NONE,
		&desc,
		D3D12_RESOURCE_STATE_GENERIC_READ,
		nullptr,
		IID_PPV_ARGS(textureUploadHeap.GetAddressOf()));

	assert(SUCCEEDED(hr));

	ComPtr<ID3D12GraphicsCommandList> resCmdList = MyEngine->GetCommandQueue()->GetResourceCommandList();
	::UpdateSubresources(
		resCmdList.Get(),
		_tex2D.Get(),
		textureUploadHeap.Get(),
		0, 0,
		static_cast<unsigned int>(subResources.size()),
		subResources.data());

	MyEngine->GetCommandQueue()->FlushResourceCommandQueue();
}

void Texture::CreateView()
{
	ComPtr<ID3D12Device> device = MyEngine->GetDevice()->GetDevice();

	D3D12_DESCRIPTOR_HEAP_DESC heapDesc = {};
	heapDesc.NumDescriptors = 1;
	heapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
	heapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	device->CreateDescriptorHeap(&heapDesc, IID_PPV_ARGS(&_srvHeap));

	_srvHandle = _srvHeap->GetCPUDescriptorHandleForHeapStart();

	D3D12_SHADER_RESOURCE_VIEW_DESC viewDesc = {};
	viewDesc.Format = _image.GetMetadata().format;
	viewDesc.ViewDimension = D3D12_SRV_DIMENSION_TEXTURE2D;
	viewDesc.Shader4ComponentMapping = D3D12_DEFAULT_SHADER_4_COMPONENT_MAPPING;
	viewDesc.Texture2D.MipLevels = 1;
	device->CreateShaderResourceView(_tex2D.Get(), &viewDesc, _srvHandle);
}
```

<br>

## 2. Mesh 클래스에서 Texture 설정
```cpp
MyEngine->GetTableDescriptorHeap()->SetShaderResourceView(_tex->GetCpuHandle(), SRV_REGISTER::t0);
```

## 2. CommandQueue 설정 및 ResourceList 생성
```cpp
ComPtr<ID3D12CommandAllocator>		_resCmdAlloc;
ComPtr<ID3D12GraphicsCommandList>	_resCmdList;
```

```cpp
// Te
device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_DIRECT, IID_PPV_ARGS(&_resCmdAlloc));
device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, _resCmdAlloc.Get(), nullptr, IID_PPV_ARGS(&_resCmdList));
```

```cpp
void CommandQueue::FlushResourceCommandQueue()
{
	_resCmdList->Close();

	ID3D12CommandList* cmdListArr[] = { _resCmdList.Get() };
	_cmdQueue->ExecuteCommandLists(_countof(cmdListArr), cmdListArr);

	WaitSync();

	_resCmdAlloc->Reset();
	_resCmdList->Reset(_resCmdAlloc.Get(), nullptr);
}
```

<br>

## 2. Root Signature 변경 및 Descriptor Table 설정
```cpp
CD3DX12_DESCRIPTOR_RANGE range[] =
{
    CD3DX12_DESCRIPTOR_RANGE(D3D12_DESCRIPTOR_RANGE_TYPE_CBV, CBV_REGISTER_COUNT, 0),
    CD3DX12_DESCRIPTOR_RANGE(D3D12_DESCRIPTOR_RANGE_TYPE_SRV, SRV_REGISTER_COUNT, 0),
};
...
_samplerDesc = CD3DX12_STATIC_SAMPLER_DESC(0);
D3D12_ROOT_SIGNATURE_DESC desc = CD3DX12_ROOT_SIGNATURE_DESC(_countof(param), param, 1, &_samplerDesc);
```

```cpp
void TableDescriptorHeap::SetShaderResourceView(D3D12_CPU_DESCRIPTOR_HANDLE srcHandle, SRV_REGISTER reg)
{
	D3D12_CPU_DESCRIPTOR_HANDLE destHandle = GetCpuHandle(reg);

	uint32 destRange = 1;
	uint32 srcRange = 1;
	MyEngine->GetDevice()->GetDevice()->CopyDescriptors(1, &destHandle, &destRange, 1, &srcHandle, &srcRange, D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV);
}
```

<br>

## 3. HLSL 변경 및 Shader 정보 추가

```cpp
...
// Texture를 전달받을 수 있는 레지스터 설정
Texture2D tex_0 : register(t0);
SamplerState sam_0 : register(s0);
// uv좌표 추가
struct VS_IN
{
	...
    float2 uv : TEXCOORD;
};

struct VS_OUT
{
	...
    float2 uv : TEXCOORD;
};

VS_OUT VS_Main(VS_IN input)
{
    VS_OUT output = (VS_OUT)0;

    output.pos = float4(input.pos, 1.f);
    output.color = input.color;
    output.uv = input.uv;

    return output;
}

float4 PS_Main(VS_OUT input) : SV_Target
{
    float4 color = tex_0.Sample(sam_0, input.uv);
    return color;
}
```

```cpp
D3D12_INPUT_ELEMENT_DESC desc[] =
{
	...
	{ "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 28, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
};
```

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)



