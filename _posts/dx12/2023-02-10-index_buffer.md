---
#layout: posts
excerpt: ""
title: "[DirectX12] 📂. Index Buffer"
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

# 🔷 Index Buffer

## 1. IndexBuffer 설정
- 색인을 담는 버퍼  

---

```cpp
ComPtr<ID3D12Resource>		_indexBuffer;
D3D12_INDEX_BUFFER_VIEW		_indexBufferView = {};
uint32						_indexCount = 0;
```

1) [D3D12_INDEX_BUFFER_VIEW](https://learn.microsoft.com/ko-kr/windows/win32/api/d3d12/ns-d3d12-d3d12_index_buffer_view)
- 인덱스(색인) 버퍼 뷰에 대해 설명
- `서술자 힙이 필요 없음`

```cpp
_indexCount = static_cast<uint32>(ibvs.size());
uint32 bufferSize = _indexCount * sizeof(Vertex);

// 1. 버퍼생성
D3D12_HEAP_PROPERTIES heapProperty = CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD);
D3D12_RESOURCE_DESC desc = CD3DX12_RESOURCE_DESC::Buffer(bufferSize);
MyEngine->GetDevice()->GetDevice()->CreateCommittedResource(
    &heapProperty,
    D3D12_HEAP_FLAG_NONE,
    &desc,
    D3D12_RESOURCE_STATE_GENERIC_READ,
    nullptr,
    IID_PPV_ARGS(&_indexBuffer));

// 2. CPU -> GPU 복사
void* indexDataBuffer = nullptr;
CD3DX12_RANGE readRange(0, 0);
_indexBuffer->Map(0, &readRange, &indexDataBuffer);
::memcpy(indexDataBuffer, &ibvs[0], bufferSize);
_indexBuffer->Unmap(0, nullptr);

// 3. indexBuffer를 사용하는 방법 묘사
_indexBufferView.BufferLocation = _indexBuffer->GetGPUVirtualAddress();     // GPU 주소
_indexBufferView.Format = DXGI_FORMAT_R32_UINT;                             // 32bit 포맷
_indexBufferView.SizeInBytes = bufferSize;                                  // 버퍼 크기
```

<br>

## 2. IndexBuffer를 활용하여 물체 그리기

```cpp
// 색인 버퍼를 입력조립기(IA)단계에 바인딩
cmdList->IASetIndexBuffer(&_indexBufferView);
...
// 색인을 이용한 기본도형 그리기
cmdList->DrawIndexedInstanced(_indexCount, 1, 0, 0, 0);	// 실제로 그려짐
```

<br>

# 📑. 참고
* [Rookiss. [C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈]Part2: 게임 수학과 DirectX12. Inflearn.](https://www.inflearn.com/course/%EC%96%B8%EB%A6%AC%EC%96%BC-3d-mmorpg-2/dashboard)
* [프랭크 D. 루나(2020). DirectX 12를 이용한 3D게임 프로그래밍 입문. 한빛미디어(주).](https://www.hanbit.co.kr/store/books/look.php?p_code=B5088646371)