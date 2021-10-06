---
#layout: posts
excerpt: ""
title: "[Civil3d 3rd Party] 📂. Profile Reverse"
categories:
  - c3d_3rdparty

toc: true
toc_sticky: true

date: 2021-10-07
last-modified_at: 2021-10-07

published: false
---

# 선형 역전에 따른 종단 역전시키기
- 개발을 하다보면 선형을 `역전(Reverse)` 시킬 때가 있다. 그때, 종단도 함께 역전시켜줘야하는데 종단엔 `Reverse()` 함수가 없다.

(Profile Reverse 없는 사진)

## 💡. 생각한 방법
### 1. `PVI`를 Reverse 시킨다.

### 2. `ProfileEntity`를 Reverse 시킨다.

## 📌. 해결방법
종단 생성 전에 선형을 먼저 역전시키면 자동으로 종단도 역전되서 만들어진다.
