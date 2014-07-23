---
layout: default
title: 在 Python 使用 Lazarus 定義的函式庫
tags: [python, lazarus, delphi]
---
<h2>{{ page.title }}</h2>

如果你已經有一坨 Lazarus 或 Delphi 的程式碼，很想要轉換到 Python 的世界，但又割捨不下原本的心血，那麼使用 Shared Library 即可輕鬆達成目標，以下藥方可配溫開水服用——

1. 用 Lazarus 編譯 Shared Library。
2. 在 Python 中讀進 Shared Library。

基本概念就是上面兩步。只要跟著接下來的步驟，你就可以在 Python 操作定義在 Lazarus 的資料結構，至於反過來的需求（在 Lazarus 中操作 Python 的資料結構），C/C++ 的經驗是使用 [python.h](https://docs.python.org/2/extending/extending.html) ，但目前還沒有完善的 Pascal 接口，等到以後有需求了再跟你說。

下面是具體步驟。

##0. 測試平台
以下平台都測試 OK 。

平台 1：

```
OS: Mac OS X 10.9.3
Python: 2.7.3
Lazarus: 1.0.14
```

平台 2:

```
OS: Mint 15 64-bit
Python: 2.7.4
Lazarus: 1.0.14
```

##1. Export Methods in Lazarus

假設我有以下資料結構：

```pascal
TCircle = class
public
  constructor Create(X, Y, Radius: Integer);
  property X: Integer;
  property Y: Integer;
  property Radius: Integer;
end;
```

雖然物件化很好，但要透過 Shared Library 溝通，資料結構得『扁平化』才行，意思是要準備一系列的 method ，將對此物件的各個 constructor 、 destructor 、 getters 、 setters 通通獨立出來，學武前的蹲馬步總是比較枯燥的，忍忍就過了：

```pascal
procedure Circle_Create(var AObject: Pointer; X, Y, Radius: Integer); cdecl;
procedure Circle_Free(AObject: Pointer); cdecl;
function Circle_X(AObject: Pointer): Integer; cdecl;
function Circle_Y(AObject: Pointer): Integer; cdecl;
function Circle_Radius(AObject: Pointer): Integer; cdecl;
```

to be continue ...
