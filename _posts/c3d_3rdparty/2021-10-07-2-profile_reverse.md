---
#layout: posts
excerpt: ""
title: "[Civil3d 3rd Party] π. Profile Reverse"
categories:
  - c3d_3rdparty

toc_label: "λͺ©μ°¨"
toc: true
toc_sticky: true

date: 2021-10-07
last-modified_at: 2021-10-15

#published: false
---

# μ ν μ­μ μ λ°λ₯Έ μ’λ¨ μ­μ μν€κΈ°
- Offset μ νμ μμ±ν  λ, Offset μ νμ μ§ν₯λ°©ν₯μ `μ­μ (Reverse)` μν¬ λκ° μλ€. κ·Έλ, μ’λ¨λ ν¨κ» μ­μ μμΌμ€μΌνλλ° μ’λ¨μ `Reverse()` ν¨μκ° μλ€.
  > **Alignment Class**
  > ![image](../../assets/images/c3d_3rdparty_img/2_profile_reverse_img/alignmentfunc.png)

  > **Profile Class**
  > ![image](../../assets/images/c3d_3rdparty_img/2_profile_reverse_img/profilefunc.png)

  ```cs
  // Offsetμ ν μμ±
  Alignment offsetAlign = Alignment.CreateOffsetAlignment(β¦); 
  // Offset μ’λ¨ μμ±
  Profile offsetProf = prof.CreateOffsetAlignment(β¦); 

  offsetAlign.Reverse();
  // μμ κ²°κ³Ό: μ νμ΄ λ°μ λ¨μ λ°λΌ μ’λ¨λ μλμΌλ‘ μ­μ  λ¨.
  // κ²°κ³Ό : μ’λ¨μ μ­μ λμ§ μμ.
  ```

<br>

## π‘. μκ°ν λ°©λ²
 1. `PVI`λ₯Ό Reverse μν¨λ€.
    ```cs
    Alignment offsetAlign = Alignment.CreateOffsetAlignment(β¦); 
    Profile offsetProf = prof.CreateOffsetAlignment(β¦);
    
    ProfilePVICollection offsetProf_pvis = offsetProf.PVIs;
    offsetProf_pvis.Reverse();
    // κ²°κ³Ό : μ’λ¨μ΄ μ­μ λμ§ μμ.
     ```

 2. `ProfileEntity`λ₯Ό Reverse μν¨λ€.
    ```cs
    Alignment offsetAlign = Alignment.CreateOffsetAlignment(β¦);
    Profile offsetProf = prof.CreateOffsetAlignment(β¦);

    ProfileEntityCollection offsetProf_entities = offsetProf.Entities;
    offsetProf_entities.Reverse();
    // κ²°κ³Ό : μ’λ¨μ΄ μ­μ λμ§ μμ.
    ```

<br>

## π. ν΄κ²°λ°©λ²
- `μ’λ¨ μμ± μ `μ μ νμ λ¨Όμ  `μ­μ `μν€λ©΄ μλμΌλ‘ μ’λ¨λ μ­μ λμ λ§λ€μ΄μ§λ€.
  ```cs
  Alignment offsetAlign = Alignment.CreateOffsetAlignment(β¦); 
  offsetAlign.Reverse();

  Profile offsetProf = prof.CreateOffsetAlignment(β¦);
  // κ²°κ³Ό : μ’λ¨μ΄ μ­μ λμ΄ μμ±λλ€.
  ```