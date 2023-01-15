---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Initialize"
categories:
    - dx12
# tag:
#     [cpp, c++, oop]
toc_label: "목차"
toc: true
toc_sticky: true
date: 2023-01-15
last_modified_at: 2023-01-15
#published: false
---
# 🔷 초기화
## 🔹 Engine
- 엔진의 핵심적인 기능을 담당하는 클래스
```cpp
class Engine
{
public:
    void Init(const HWND& hwnd, const int32 width, const int32 height, bool windowed);

public:
    void Render();
    void Start();
    void End();
    ...

private:
    ...
    // 그려질 화면 크기 정보를 가짐
	D3D12_VIEWPORT	_viewport = {};		
	D3D12_RECT		_scissorRect = {};	

	shared_ptr<class Device>			_device;
	shared_ptr<class CommandQueue>		_cmdQueue;
	shared_ptr<class SwapChain>			_swapChain;
	shared_ptr<class DescriptorHeap>	_descHeap;
};
```

```cpp
void Engine::Init(const HWND& hwnd, const int32 width, const int32 height, bool windowed)
{
    ...
    // 그려질 화면 크기 설정
	_viewport = { 0,0,static_cast<FLOAT>(window.width), static_cast<FLOAT>(window.height), 0.0f, 1.0f };
	_scissorRect = CD3DX12_RECT(0, 0, window.width, window.height);
	// ptr
	_device = make_shared<Device>();
	_cmdQueue = make_shared<CommandQueue>();
	_swapChain = make_shared<SwapChain>();
	_descHeap = make_shared<DescriptorHeap>();
	// init
	_device->Init();
	_cmdQueue->Init(_device->GetDevice(), _swapChain, _descHeap);
	_swapChain->Init(window, _device->GetDXGI(), _cmdQueue->GetCommandQueue());
	_descHeap->Init(_device->GetDevice(), _swapChain);
}

void Engine::Render()
{
	Start();
	// TODO : 렌더링 요소 추가
	End();
}

void Engine::Start()
{
	_cmdQueue->RenderBegin(&_viewport, &_scissorRect);
}

void Engine::End()
{
	_cmdQueue->RenderEnd();
}
```

<br>

## 🔹 Device
- 인력 사무소
```cpp
class Device
{
public:
	void Init();
    ...

private:
	ComPtr<ID3D12Debug>		_debug;
	ComPtr<IDXGIFactory>	_dxgi; // display
	ComPtr<ID3D12Device>	_device; // GPU
};
```

```cpp
void Device::Init()
{
#ifdef _DEBUG
	::D3D12GetDebugInterface(IID_PPV_ARGS(&_debug));
	_debug->EnableDebugLayer();
#endif

	::CreateDXGIFactory(IID_PPV_ARGS(&_dxgi));
	::D3D12CreateDevice(nullptr, D3D_FEATURE_LEVEL_11_0, IID_PPV_ARGS(&_device));
}
```

<br>

## 🔹 CommandQueue
- 외주 일감 목록
```cpp
class CommandQueue
{
public: 
	void Init(ComPtr<ID3D12Device> device, shared_ptr<SwapChain> swapChain, shared_ptr<DescriptorHeap> descHeap);
    ...

public:
	void WaitSync();
	void RenderBegin(const D3D12_VIEWPORT* viewport, const D3D12_RECT* rect);
	void RenderEnd();
	...

private:
	ComPtr<ID3D12CommandQueue>		_cmdQueue;
	ComPtr<ID3D12CommandAllocator>	_cmdAlloc;
	ComPtr<ID3D12GraphicsCommandList>		_cmdList;

	ComPtr<ID3D12Fence>				_fence;
	uint32							_fenceValue = 0;
	HANDLE							_fenceEvent = INVALID_HANDLE_VALUE;

	shared_ptr<SwapChain>			_swapChain;
	shared_ptr<DescriptorHeap>		_descHeap;
};
```

```cpp
void CommandQueue::Init(ComPtr<ID3D12Device> device, shared_ptr<SwapChain> swapChain, shared_ptr<DescriptorHeap> descHeap)
{
	_swapChain = swapChain;
	_descHeap = descHeap;

	D3D12_COMMAND_QUEUE_DESC queueDesc = {};
	queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
	queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;

	device->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&_cmdQueue));
	device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_DIRECT, IID_PPV_ARGS(&_cmdAlloc));
	device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, _cmdAlloc.Get(), nullptr, IID_PPV_ARGS(&_cmdList));

	device->CreateFence(0, D3D12_FENCE_FLAG_NONE, IID_PPV_ARGS(&_fence));
	_fenceEvent = ::CreateEvent(nullptr, FALSE, FALSE, nullptr);	
}

void CommandQueue::WaitSync()
{
	_fenceValue++;

	_cmdQueue->Signal(_fence.Get(), _fenceValue);

	if (_fence->GetCompletedValue() < _fenceValue)
	{
		_fence->SetEventOnCompletion(_fenceValue, _fenceEvent);
		::WaitForSingleObject(_fenceEvent, INFINITE);
	}
}

void CommandQueue::RenderBegin(const D3D12_VIEWPORT* viewport, const D3D12_RECT* rect)
{
	_cmdAlloc->Reset();
	_cmdList->Reset(_cmdAlloc.Get(), nullptr);

	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_PRESENT,
		D3D12_RESOURCE_STATE_RENDER_TARGET);
	_cmdList->ResourceBarrier(1, &barrier);

	_cmdList->RSSetViewports(1, viewport);
	_cmdList->RSSetScissorRects(1, rect);

	D3D12_CPU_DESCRIPTOR_HANDLE backBufferView = _descHeap->GetBackBufferView();
	_cmdList->ClearRenderTargetView(backBufferView, Colors::BlanchedAlmond, 0, nullptr);
	_cmdList->OMSetRenderTargets(1, &backBufferView, FALSE, nullptr);
}

void CommandQueue::RenderEnd()
{
	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_RENDER_TARGET,
		D3D12_RESOURCE_STATE_PRESENT);

	_cmdList->ResourceBarrier(1, &barrier);
	_cmdList->Close();

	ID3D12CommandList* cmdListArr[] = { _cmdList.Get() };
	_cmdQueue->ExecuteCommandLists(_countof(cmdListArr), cmdListArr);

	_swapChain->Present();

	WaitSync();

	_swapChain->SwapIndex();
}
```

<br>

## 🔹 SwaphChain
- 교환 사슬
```cpp
class SwapChain
{
public:
	void Init(const WindowInfo& window, ComPtr<IDXGIFactory> dxgi, ComPtr<ID3D12CommandQueue> cmdQueue);

public:
	void Present();
	void SwapIndex();
	...

private:
	ComPtr<IDXGISwapChain>	_swapChain;
	ComPtr<ID3D12Resource>	_renderTargets[SWAP_CHAIN_BUFFER_COUNT];
	uint32					_backBufferIndex = 0;
};
```

```cpp
void SwapChain::Init(const WindowInfo& window, ComPtr<IDXGIFactory> dxgi, ComPtr<ID3D12CommandQueue> cmdQueue)
{
	_swapChain.Reset();

	DXGI_SWAP_CHAIN_DESC chainDesc = {};
	chainDesc.BufferDesc.Width = static_cast<uint32>(window.width);
	chainDesc.BufferDesc.Height = static_cast<uint32>(window.height);
	chainDesc.BufferDesc.RefreshRate.Numerator = 60;							// 화면 갱신 비율
	chainDesc.BufferDesc.RefreshRate.Denominator = 1;							// 화면 갱신 비율
	chainDesc.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM;					// 버퍼의 디스플레이 형식
	chainDesc.BufferDesc.ScanlineOrdering = DXGI_MODE_SCANLINE_ORDER_UNSPECIFIED;
	chainDesc.BufferDesc.Scaling = DXGI_MODE_SCALING_UNSPECIFIED;
	chainDesc.SampleDesc.Count = 1;												// 멀티 샘플링 OFF
	chainDesc.SampleDesc.Quality = 0;
	chainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;					// 후면 버퍼에 렌더링할 것 
	chainDesc.BufferCount = SWAP_CHAIN_BUFFER_COUNT;							// 전면+후면 버퍼
	chainDesc.OutputWindow = window.hwnd;
	chainDesc.Windowed = window.windowed;
	chainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_DISCARD;						// 전면 후면 버퍼 교체 시 이전 프레임 정보 버림
	chainDesc.Flags = DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH;
	
	dxgi->CreateSwapChain(cmdQueue.Get(), &chainDesc, &_swapChain);

	for (int32 i = 0; i < SWAP_CHAIN_BUFFER_COUNT; i++)
		_swapChain->GetBuffer(i, IID_PPV_ARGS(&_renderTargets[i]));
}

void SwapChain::Present()
{
	_swapChain->Present(0, 0);
}

void SwapChain::SwapIndex()
{
	_backBufferIndex = (_backBufferIndex + 1) % SWAP_CHAIN_BUFFER_COUNT;
}
```


<br>

## 🔹 DescriptorHeap
- 기안서
```cpp
class DescriptorHeap
{
public:
	void Init(ComPtr<ID3D12Device> device, shared_ptr<class SwapChain> swapChain);
    ...

private:
	ComPtr<ID3D12DescriptorHeap>	_rtvHeap;
	uint32							_rtvHeapSize = 0;
	D3D12_CPU_DESCRIPTOR_HANDLE		_rtvHandle[SWAP_CHAIN_BUFFER_COUNT];

	shared_ptr<class SwapChain>		_swapChain;
};
```

```cpp
void DescriptorHeap::Init(ComPtr<ID3D12Device> device, shared_ptr<class SwapChain> swapChain)
{
	_swapChain = swapChain;

	_rtvHeapSize = device->GetDescriptorHandleIncrementSize(D3D12_DESCRIPTOR_HEAP_TYPE_RTV);

	D3D12_DESCRIPTOR_HEAP_DESC rtvDesc = {};
	rtvDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_RTV;
	rtvDesc.NumDescriptors = SWAP_CHAIN_BUFFER_COUNT;
	rtvDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	rtvDesc.NodeMask = 0;

	device->CreateDescriptorHeap(&rtvDesc, IID_PPV_ARGS(&_rtvHeap));

	D3D12_CPU_DESCRIPTOR_HANDLE rtvHeapBegin = _rtvHeap->GetCPUDescriptorHandleForHeapStart();

	for (int i = 0; i < SWAP_CHAIN_BUFFER_COUNT; i++)
	{
		_rtvHandle[i] = CD3DX12_CPU_DESCRIPTOR_HANDLE(rtvHeapBegin, i * _rtvHeapSize);
		device->CreateRenderTargetView(swapChain->GetRenderTarget(i).Get(), nullptr, _rtvHandle[i]);
	}
}
```


<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)