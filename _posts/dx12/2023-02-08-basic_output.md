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

---

1) [D3D12_VERTEX_BUFFER_VIEW](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/ns-d3d12-d3d12_vertex_buffer_view)
- 정점 버퍼 뷰에 대해 설명한다.

```cpp
ComPtr<ID3D12Resource>		_vertexBuffer;
D3D12_VERTEX_BUFFER_VIEW	_vertexBufferView = {};
uint32						_vertexCount;
```

---

1) VertexBuffer 생성 및 VBV생성
> `정점 버퍼(Vertex Buffer)`  
> - 정점들을 저장하는 버퍼  
>> - 정적 기하구조(프레임마다 변하지 않는 기하구조)에선 정범 버퍼들을 기본 힙(D3D12_HEAP_TYPE_DEFAULT)에 넣는다. (초기화 후, GPU만 버퍼의 정점을 읽기 때문에)  
>> - 임시 업로드용 버퍼(D3D12_HEAP_TYPE_UPLOAD) 자원을 생성 후 기본 힙으로 복사한다.  

```cpp
_vertexCount = static_cast<uint32>(vec.size()); 
uint32 bufferSize = _vertexCount * sizeof(Vertex);

// (1) 버퍼생성
D3D12_HEAP_PROPERTIES heapProperty = CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD);
// 범용 GPU자원으로서의 버퍼에서 너비(width)는 버퍼의 바이트 개수를 뜻한다.
// > ex) 64개의 float를 담는 버퍼의 너비 = 64 * sizeof(float)
D3D12_RESOURCE_DESC desc = CD3DX12_RESOURCE_DESC::Buffer(bufferSize);
MyEngine->GetDevice()->GetDevice()->CreateCommittedResource(
	&heapProperty,
	D3D12_HEAP_FLAG_NONE,
	&desc,
	D3D12_RESOURCE_STATE_GENERIC_READ,
	nullptr,
	IID_PPV_ARGS(&_vertexBuffer));

// (2) 정점정보를 CPU에서 GPU로 복사
void* vertexDataBuffer = nullptr;
CD3DX12_RANGE readRange(0, 0);
_vertexBuffer->Map(0, &readRange, &vertexDataBuffer);
::memcpy(vertexDataBuffer, &vec[0], bufferSize);
_vertexBuffer->Unmap(0, nullptr);

// (3) VertextBufferView 설정 (vertexBuffer를 사용하는 방법 묘사)
// 정점 버퍼를 파이프라인에 묶기 위해 정점 자원을 서술하는 정점 버퍼 뷰가 필요하다.
// ⚠️ 정점 버퍼 뷰는 서술자 힙이 필요없다.
_vertexBufferView.BufferLocation = _vertexBuffer->GetGPUVirtualAddress();	// GPU 주소
_vertexBufferView.StrideInBytes = sizeof(Vertex);							// 데이터(정점) 1개 크기
_vertexBufferView.SizeInBytes = bufferSize;									// 버퍼 크기
```

2) 메시를 그려지게 하기 위한 설정 및 작업

```cpp
// (1) 정점의 기본도형 위상구조 상태 결정
cmdList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);		// 정점 3개(삼각형) 단위 설정
// (2) 정점 버퍼를 입력조립기(IA)단계로 공급
cmdList->IASetVertexBuffers(0, 1, &_vertexBufferView);						// Slot: (0~15)	// 버퍼 주소(위치)
// (3) 정점들이 실제로 그려지게 명령
cmdList->DrawInstanced(_vertexCount, 1, 0, 0);
```

<br>

## 2. RootSignature Class
- 루트 서명을 담당하는 클래스
> `루트 서명(root signature)`  
> - 그리기 호출 전, 렌더링 파이프라인에 묶어야하는 자원 명세
> - 그리기 호출 전, 셰이더 입력 레지스터와 대응 명세
> - ⚠️ 실제 자원을 묶지 않고 오직 명세만 한다.
> - ⚠️ 서로 다른 셰이더를 사용한다면, 루트 서명도 달라야한다.
> - `루트 상수(root constant)` / `루트 서술자(root descriptor)` / `서술자 테이블 (descriptor table)`  

---

```cpp
ComPtr<ID3D12RootSignature> _signature;
```

1) [ID3D12RootSignature](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/nn-d3d12-id3d12rootsignature)
- 그래픽 파이프라인에 바인딩 된 리소스를 정의  
- 앱에서 구성되고 명령 목록을 셰이더에 필요한 리소스에 연결  
- 주어진 그리기 호출에서 셰이더들이 기대하는 자원들을 서술하는 루트 매개변수들의 배열로 정의  

```cpp
D3D12_ROOT_SIGNATURE_DESC desc = CD3DX12_ROOT_SIGNATURE_DESC(D3D12_DEFAULT);
desc.Flags = D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT;

ComPtr<ID3DBlob> blobSignature;
ComPtr<ID3DBlob> blobError;
// ID3D12RootSignature 객체 생성
::D3D12SerializeRootSignature(&desc, D3D_ROOT_SIGNATURE_VERSION_1, &blobSignature, &blobError);
device->CreateRootSignature(0, blobSignature->GetBufferPointer(), blobSignature->GetBufferSize(), IID_PPV_ARGS(&_signature));
```

<br>

## 3. Shader Class
- 파이프라인 상태객체(PSO : Pipeline State Object)를 관리하는 클래스  

---

1) [ID3D12PipelineState](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/nn-d3d12-id3d12pipelinestate)  
- 현재 설정된 모든 셰이더  

---

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

1) [ID3DBlob]()  
- 컴파일 오류가 발생한 경우, 오류 메시지와 경고 메시지 문자열을 담는 구조체  

2) [D3D12_GRAPHICS_PIPELINE_STATE_DESC](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/ns-d3d12-d3d12_graphics_pipeline_state_desc)
- 그래픽 파이프라인 상태 개체를 설명  

3) [ID3D12PipelineState](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/nn-d3d12-id3d12pipelinestate)
- 현재 설정된 모든 셰이더의 상태와 특정 고정 함수 상태 개체를 나타냄  
- 파이프라인 상태 객체(pipeline state object : PSO)라 부르는 집합체  

---

1) VS, PS를 런타임 컴파일
- hlsl코드를 컴파일

```cpp
uint32 compileFlag = 0;
#ifdef _DEBUG
	compileFlag = D3DCOMPILE_DEBUG | D3DCOMPILE_SKIP_OPTIMIZATION;
#endif

	if (FAILED(::D3DCompileFromFile(path.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE, name.c_str(), version.c_str(), compileFlag, 0, &blob, &_errBlob)))
	{
		::MessageBoxA(nullptr, "Shader Create Failed !", nullptr, MB_OK);
	}

	shaderByteCode = { blob->GetBufferPointer(), blob->GetBufferSize() };
```

2) 입력 배치 서술
- HLSL의 입력에 해당하는 값의 Format을 설정
- 파이프라인에 공급되는 정점들의 특성과 정점 셰이더의 매개변수들 사이의 `연관 관계`를 정의

```cpp
// hlsl의 VS_IN과 맵핑되는 정보
D3D12_INPUT_ELEMENT_DESC desc[] =
{
	{ "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
	{ "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 }
};
```

> ⚠️ 정점자료와 입력 서명이 적확히 일치할 필요 없음 (모든 입력을 제공하지 않은 상태 제외) 
> --- 
> 1) 정점 셰이더가 사용하지 않는 추가적인 정보를 정점 자료가 제공하는 것은 오류가 아님  
>> 
>> ```cpp
>> D3D12_INPUT_ELEMENT_DESC desc[] =
>> {
>>	{ "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
>>	{ "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
>>   { "NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 28, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 }
>> };
>> ```
>> ```cpp
>> struct VS_IN
>> {
>>     float3 pos : POSITION;
>>     float4 color : COLOR;
>> };
>> ```
>>
> 2) 정점 구조체와 입력 서명의 정점 특성들이 부합하긴 하지만, 구체적인 형식이 다른 경우, Direct3D에서 입력 레지스터 비트들의 재해석(reinterpret)을 허용하기에 오류가 아님 (경고 메세지 발생)  
>> ```cpp
>> D3D12_INPUT_ELEMENT_DESC desc[] =
>> {
>>	{ "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
>>	{ "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 },
>> };
>> ```
>> ```cpp
>> struct VS_IN
>> {
>>     float3 pos : POSITION;
>>     int4 color : COLOR;
>> };
>> ```

3) PSO 객체(ID3D12PipelineState) 생성

```cpp
// 파이프라인 상태 서술
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
// PSO 객체 생성
MyEngine->GetDevice()->GetDevice()->CreateGraphicsPipelineState(&_pipelineDesc, IID_PPV_ARGS(&_pipelineState));
```

4) 파이프라인 설정

```cpp
MyEngine->GetCommandQueue()->GetCommandList()->SetPipelineState(_pipelineState.Get());
```

<br>

## 4. HLSL(high level shading language)
- 고수준 셰이딩 언어

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