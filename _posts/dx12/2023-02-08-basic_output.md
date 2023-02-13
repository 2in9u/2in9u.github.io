---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. 기본 출력 (삼각형)"
categories:
    - dx12
# tag:
#     [cpp, c++, oop]
toc_label: "목차"
toc: true
toc_sticky: true
date: 2023-02-08
last_modified_at: 2023-02-08

#published: false
---

# 🔷 기본 입출력

## 1. Mesh Class
- 모델의 정점의 정보를 가지며, 모델의 렌더링 명령을 넣어주는 클래스

```cpp
#pragma once
class Mesh
{
public:
	void Init(vector<Vertex>& vec);

public:
	void Render();

private:
	ComPtr<ID3D12Resource>		_vertexBuffer;
	D3D12_VERTEX_BUFFER_VIEW	_vertexBufferView = {};
	uint32						_vertexCount;
};
```

```cpp
#include "pch.h"
#include "Mesh.h"
#include "Engine.h"
#include "Device.h"
#include "CommandQueue.h"

void Mesh::Init(vector<Vertex>& vec)
{
	_vertexCount = static_cast<uint32>(vec.size());
	uint32 bufferSize = _vertexCount * sizeof(Vertex);

	// 1. 버퍼생성
	D3D12_HEAP_PROPERTIES heapProperty = CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD);
	D3D12_RESOURCE_DESC desc = CD3DX12_RESOURCE_DESC::Buffer(bufferSize);
	MyEngine->GetDevice()->GetDevice()->CreateCommittedResource(
		&heapProperty,
		D3D12_HEAP_FLAG_NONE,
		&desc,
		D3D12_RESOURCE_STATE_GENERIC_READ,
		nullptr,
		IID_PPV_ARGS(&_vertexBuffer));

	// 2. CPU -> GPU 복사
	void* vertexDataBuffer = nullptr;
	CD3DX12_RANGE readRange(0, 0);
	_vertexBuffer->Map(0, &readRange, &vertexDataBuffer);
	::memcpy(vertexDataBuffer, &vec[0], bufferSize);
	_vertexBuffer->Unmap(0, nullptr);

	// 3. vertexBuffer를 사용하는 방법 묘사
	_vertexBufferView.BufferLocation = _vertexBuffer->GetGPUVirtualAddress();	// GPU 주소
	_vertexBufferView.StrideInBytes = sizeof(Vertex);							// 데이터(정점) 1개 크기
	_vertexBufferView.SizeInBytes = bufferSize;									// 버퍼 크기
}

void Mesh::Render()
{
	ComPtr<ID3D12GraphicsCommandList> cmdList = MyEngine->GetCommandQueue()->GetCommandList();
	cmdList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);								// 정점 3개(삼각형) 단위 설정
	cmdList->IASetVertexBuffers(0, 1, &_vertexBufferView);									// Slot: (0~15)	// 버퍼 주소(위치)
	cmdList->DrawInstanced(_vertexCount, 1, 0, 0);	// 실제로 그려짐
}
```

<br>

## 2. Shader Class

```cpp
#pragma once
class Shader
{
public:
	void Init(const wstring& path);

public:
	void Update();

private:
	void CreateShader(const wstring& path, const string& name, const string& version, ComPtr<ID3DBlob>& blob, D3D12_SHADER_BYTECODE& shaderByteCode);
	void CreateVertexShader(const wstring& path, const string& name, const string& version);
	void CreatePixelShader(const wstring& path, const string& name, const string& version);

private:
	ComPtr<ID3DBlob>	_vsBlob;
	ComPtr<ID3DBlob>	_psBlob;
	ComPtr<ID3DBlob>	_errBlob;

	ComPtr<ID3D12PipelineState>			_pipelineState;
	D3D12_GRAPHICS_PIPELINE_STATE_DESC	_pipelineDesc = {};
};
```

```cpp
#include "pch.h"
#include "Shader.h"

#include "Engine.h"
#include "CommandQueue.h"
#include "RootSignature.h"
#include "Device.h"

void Shader::Init(const wstring& path)
{
	CreateVertexShader(path, "VS_Main", "vs_5_0");
	CreatePixelShader(path, "PS_Main", "ps_5_0");

	// hlsl의 vs_IN과 맵핑되는 정보
	D3D12_INPUT_ELEMENT_DESC desc[] =
	{
		{ "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
		{ "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 }
	};

	_pipelineDesc.InputLayout = { desc, _countof(desc) };
	_pipelineDesc.pRootSignature = MyEngine->GetRootSignature()->GetRootSignature().Get(); // RootSignature 연동

	_pipelineDesc.RasterizerState = CD3DX12_RASTERIZER_DESC(D3D12_DEFAULT);
	_pipelineDesc.BlendState = CD3DX12_BLEND_DESC(D3D12_DEFAULT);
	_pipelineDesc.DepthStencilState.DepthEnable = FALSE;
	_pipelineDesc.DepthStencilState.StencilEnable = FALSE;
	_pipelineDesc.SampleMask = UINT_MAX;
	_pipelineDesc.PrimitiveTopologyType = D3D12_PRIMITIVE_TOPOLOGY_TYPE_TRIANGLE;
	_pipelineDesc.NumRenderTargets = 1;
	_pipelineDesc.RTVFormats[0] = DXGI_FORMAT_R8G8B8A8_UNORM;
	_pipelineDesc.SampleDesc.Count = 1;

	MyEngine->GetDevice()->GetDevice()->CreateGraphicsPipelineState(&_pipelineDesc, IID_PPV_ARGS(&_pipelineState));
}

void Shader::Update()
{
	MyEngine->GetCommandQueue()->GetCommandList()->SetPipelineState(_pipelineState.Get());
}

void Shader::CreateShader(const wstring& path, const string& name, const string& version, ComPtr<ID3DBlob>& blob, D3D12_SHADER_BYTECODE& shaderByteCode)
{
	uint32 compileFlag = 0;
#ifdef _DEBUG
	compileFlag = D3DCOMPILE_DEBUG | D3DCOMPILE_SKIP_OPTIMIZATION;
#endif

	if (FAILED(::D3DCompileFromFile(path.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE, name.c_str(), version.c_str(), compileFlag, 0, &blob, &_errBlob)))
	{
		::MessageBoxA(nullptr, "Shader Create Failed !", nullptr, MB_OK);
	}

	shaderByteCode = { blob->GetBufferPointer(), blob->GetBufferSize() };
}

void Shader::CreateVertexShader(const wstring& path, const string& name, const string& version)
{
	CreateShader(path, name, version, _vsBlob, _pipelineDesc.VS); // VS
}

void Shader::CreatePixelShader(const wstring& path, const string& name, const string& version)
{
	CreateShader(path, name, version, _psBlob, _pipelineDesc.PS); // PS
}
```
<br>

## 3. RootSignature Class
```cpp
#pragma once
class RootSignature
{
public:
	void Init(ComPtr<ID3D12Device> device);

public:
	ComPtr<ID3D12RootSignature> GetRootSignature() { return _signature; }

private:
	ComPtr<ID3D12RootSignature> _signature;
};
```

```cpp
#include "pch.h"
#include "RootSignature.h"

void RootSignature::Init(ComPtr<ID3D12Device> device)
{
	D3D12_ROOT_SIGNATURE_DESC desc = CD3DX12_ROOT_SIGNATURE_DESC(D3D12_DEFAULT);
	desc.Flags = D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT;

	ComPtr<ID3DBlob> blobSignature;
	ComPtr<ID3DBlob> blobError;
	::D3D12SerializeRootSignature(&desc, D3D_ROOT_SIGNATURE_VERSION_1, &blobSignature, &blobError);
	device->CreateRootSignature(0, blobSignature->GetBufferPointer(), blobSignature->GetBufferSize(), IID_PPV_ARGS(&_signature));
}
```
<br>

## 4. HLSL
```cpp
struct VS_IN
{
    float3 pos : POSITION;
    float4 color : COLOR;
};

struct VS_OUT
{
    float4 pos : SV_Position;
    float4 color : COLOR;
};

VS_OUT VS_Main(VS_IN input)
{
    VS_OUT output = (VS_OUT)0;

    output.pos = float4(input.pos, 1.f);
    output.color = input.color;

    return output;
}

float4 PS_Main(VS_OUT input) : SV_Target
{
    return input.color;
}
```

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)