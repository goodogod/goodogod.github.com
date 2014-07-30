---
layout: default
title: 在 Python 使用 Lazarus 定義的函式庫
tags: [python, lazarus, delphi]
---
<h2>{{ page.title }}</h2>

如果你已經有一坨 Lazarus 或 Delphi 的程式碼，很想要轉換到 Python 的世界，但又割捨不下原本的心血，那麼使用 Shared Library 即可輕鬆達成目標，以下藥方可配溫開水服用——

1. 用 Lazarus 編譯 Shared Library。
2. 在 Python 中讀進 Shared Library。

基本概念就是上面兩步。只要跟著接下來的步驟，你就可以在 Python 操作定義在 Lazarus 的資料結構，至於反過來的需求（在 Lazarus 中操作 Python 的資料結構），C/C++ 的經驗是使用 [python.h](https://docs.python.org/2/extending/extending.html) ，但目前還沒有完善的 Pascal 接口，之後有心得再發上來。

下面是具體步驟：

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

其中 `cdecl` 是 C 語言的[函式呼叫慣例](http://zh.wikipedia.org/wiki/X86%E8%B0%83%E7%94%A8%E7%BA%A6%E5%AE%9A)，與 Pascal 預設的 `register` 不同，故需明確宣告。

為什麼要使用 C 的 呼叫慣例呢？這是因為 Python 天生就可以讀 C 編譯出來的 shared library ，而剛好 Free Pascal 能夠與 C 相容，所以只要 exports method 的參數都[與 C 對應](http://wiki.freepascal.org/Pascal_for_C_users)，Python 都可正常呼叫。

##2. Build Shared Library
接下來在 Lazarus 中開啟一個 Library 工程，將上面的 source code use 進來，然後在 *yourproject*.lpr 的 exports 欄位中，選擇要讓 shared library 使用者呼叫的 methods：

```pascal
exports
  Circle_Create, Circle_Free, Circle_X, ...
end.
```

然後就編譯之！編譯出來的 library 會放在 project 根目錄中，其命名是由 `Project > Project Options > Paths > Target file name` 決定，如果不希望 lazarus 幫你加上慣例的前後綴字，可將 `Apply conventions` 的勾勾取消。

##3. Load Shared Library in Python
接下來在 Python 端，用以下程式碼將讀 library 的行為用物件包起來，讓使用上跟一般物件沒兩樣：（使用者體驗！）

```python

# 所有用到的工具都在這了
import ctypes

# 將 library 讀進來, 在 linux/mac 中使用 cdll, 在 windows 使用 windll
# 之所以放在 module 層級是因為, shared library 只需要讀一次
lib_handle = ctypes.cdll.LoadLibrary("./library.so")

# 如果希望呼叫時 python 幫你做型別檢查, 可使用 argtypes
# ShapeCircle_Create(pointer, integer, integer, integer);
lib_handle.Circle_Create.argtypes = [ctypes.c_void_p, 
                                     ctypes.c_int,
                                     ctypes.c_int,
                                     ctypes.c_int]

# ShapeCircle(pointer): integer;                                         
lib_handle.Circle_Radius.argtypes = [ctypes.c_void_p]

# 同樣的, 回傳值也可以檢查
lib_handle.Circle_Radius.restype = ctypes.c_int

# 建立 Circle 的包裝
class Circle:
    def __init__(self, x, y, radius):
        # 建立 library 真正操作的物件, 指標類型的形態需使用 c_void_p
        self._lib_object = ctypes.c_void_p(0)
        
        # 呼叫 library exports 的 constructor
        # ctypes.byref: 如果 library method 中有參數為 
        # call by address/reference, 在 python 端
        # 需使用 byref, 這是因為 python 預設都是 call by value.
        lib_handle.Circle_Create(ctypes.byref(self._lib_object), x, y, radius)
        
    def __del__(self):
    """使用 destructor 將內部資源清除"""
        lib_handle.Circle_Free(self._lib_object)
        
    def radius(self):
        return lib_handle.Circle_Radius(self._lib_object)
        
        
# 輕巧地使用它吧!
circle = Circle(10, 10, 50)
print "radius: " + str(circle.radius())

```


##參考資料
* [Free Pascal Type 與 C 的對應表](http://wiki.freepascal.org/Pascal_for_C_users)
* [Free Pascal Libraries](http://wiki.freepascal.org/Lazarus/FPC_Libraries)
* [Free Pascal shared library hint](http://wiki.freepascal.org/shared_library)
* [cypes documentation](https://docs.python.org/2/library/ctypes.html)
* [Python 與 C 結合](http://www.ibm.com/developerworks/cn/linux/l-cn-pythonandc/)


 
