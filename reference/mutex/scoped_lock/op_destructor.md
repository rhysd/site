# デストラクタ
* mutex[meta header]
* std[meta namespace]
* scoped_lock[meta class]
* function[meta id-type]
* cpp17[meta cpp]

```cpp
~scoped_lock();
```

## 概要
ロックを手放す


## 効果
保持している複数のミューテックスを、先頭から順番に`m.unlock()`を呼び出していく。


## バージョン
### 言語
- C++17

### 処理系
- [Clang, C++17 mode](/implementation.md#clang): 5.0
- [GCC, C++17 mode](/implementation.md#gcc): 7.3
- [Visual C++](/implementation.md#visual_cpp): ??
