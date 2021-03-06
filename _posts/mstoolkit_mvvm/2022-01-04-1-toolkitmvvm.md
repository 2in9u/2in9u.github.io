---
#layout: posts
excerpt: ""
title: "[Mvvm] ๐. Mvvm Toolkit"

categories:
  - mstoolkit_mvvm

toc_label: "๋ชฉ์ฐจ"
toc: true
toc_sticky: true

date: 2022-01-04
last-modified_at: 2022-01-04

#published: false
---

# 1. Microsoft.Toolkit.Mvvm
- MVVMํจํด ๊ตฌํ์ ์ํ ๋ฐฉ๋ฒ์ ์ฐพ๋ค๊ฐ ๋ฐ๊ฒฌํ๊ฒ ๋ MVVM ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ด๋ค.
- Visual Studio์ `Manage NuGet Package(NuGet ํจํค์ง ๊ด๋ฆฌ)`๋ก ์ค์น ํ  ์ ์๋ค.
  > ![image](../../assets/images/mstoolkit_mvvm_img/1_mvvmtoolkit/NuGetPackage.png)
  > - `Microsoft.Toolkit.Mvvm`๊ณผ `CommunityToolkit.Mvvm`์ด ์กด์ฌํ๋ค. ์ ๊ณตํ๋ ๊ธฐ๋ฅ์ ๊ฐ์ ๋ฏ ํ์ง๋ง, ํ์ผ์ ํฌ๊ธฐ๋ CommunityToolkit.Mvvm์ด ๋ ์ ๋ค
  > - ์ฐธ๊ณ ํ๋ ์ฌ์ดํธ์์ Microsoft.Toolkit.Mvvm์ ์ฌ์ฉํ๊ธฐ ๋๋ฌธ์ ์ผ๋จ ๊ฐ์ ๊ฒ์ผ๋ก ์งํํ๋ค.

```csharp
using Microsoft.Toolkiy.Mvvm;
```

<br>

# 2. ์ ๊ณตํ๋ ๊ธฐ๋ฅ

### 1. Microsoft.Toolkit.Mvvm.`ComponentModel`
- ๋ฐ์ธ๋ฉ๊ณผ ๊ด๋ จ๋ ๊ธฐ๋ฅ์ ์ ๊ณต
  - ObservableObject
  - ObservableRecipient
  - ObservableValidator
  
### 2. Microsoft.Toolkit.Mvvm.`DependencyInjection`
- ์ข์์ฑ ์ฃผ์๊ณผ ๊ด๋ จ๋ ๊ธฐ๋ฅ์ ์ ๊ณต
  - Ioc
  
### 3. Microsoft.Toolkit.Mvvm.`Input`
- ์ปค๋งจ๋ ๊ด๋ จ ๊ธฐ๋ฅ์ ์ ๊ณต
  - RelayCommand
  - RelayCommand<T>
  - AsyncRelayCommand
  - AsyncRelayCommand<T>
  - IRelayCommand
  - IRelayCommand<int T>
  - IAsyncRelayCommand
  - IAsyncRelayCommand<in T>
  
### 4. Microsoft.Toolkit.Mvvm.`Messaging`
- ๋ฉ์ ์  ๊ด๋ จ ๊ธฐ๋ฅ ์ ๊ณต
  - IMessenger
  - WeakReferenceMessenger
  - StrongReferenceMessenger
  - IRecipient<TMessage>
  - MessageHandler<TRecipient, TMessage>
  
### 5. Microsoft.Toolkit.Mvvm.`Messaging.Messages`
- ๋ฉ์ธ์ง ํด๋์ค ์ธํฐํ์ด์ค ์ ๊ณต
  - PropertyChangedMessage<T>
  - RequestMessage<T>
  - AsyncRequestMessage<T>
  - CollectionRequestMessage<T>
  - AsyncCollectionRequestMessage<T>
  - ValueChangedMessage<T>

<br>

# ๐. ์ฐธ๊ณ 
* [Introduction to the MVVM Toolkit](https://docs.microsoft.com/en-us/windows/communitytoolkit/mvvm/introduction)
* [CommunityToolkit/MVVM-Samples](https://github.com/CommunityToolkit/MVVM-Samples)
* [Connor Park. MVVM Toolkit ์ฌ์ฉ ๊ฐ์ด๋.](https://kaki104.tistory.com/674)
