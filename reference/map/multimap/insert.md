# insert
* map[meta header]
* std[meta namespace]
* multimap[meta class]
* function[meta id-type]

```cpp
iterator insert(const value_type& x);                          // (1)
iterator insert(value_type&& value);                           // (1) C++17

template <class P>
iterator insert(P&& x);                                        // (2) C++11

iterator insert(iterator position, const value_type& x);       // (3) C++03
iterator insert(const_iterator position, const value_type& x); // (3) C++11
iterator insert(const_iterator hint, value_type&& value);      // (3) C++17

template <class P>
iterator insert(const_iterator position, P&& x);               // (4) C++11

template <class InputIterator>
void insert(InputIterator first, InputIterator last);          // (5)

void insert(initializer_list<value_type> init);                // (6) C++11

iterator insert(node_type&& nh);                               // (7) C++17
iterator insert(const_iterator hint, node_type&& nh);          // (8) C++17
```
* initializer_list[link /reference/initializer_list/initializer_list.md]

## 概要
新しく一つの要素(引数 `x`, `y`を使う)、要素のシーケンス(入力イテレータまたは `initializer_list` を使う)または[ノードハンドル](/reference/node_handle/node_handle.md)を挿入することにより、 `multimap` コンテナを拡張する。

これは、挿入された要素の数だけコンテナの [`size()`](/reference/map/multimap/size.md) を増やす。

内部的に `multimap` コンテナは、コンストラクト時に指定された比較オブジェクトによって要素を下位から上位へとソートして保持する。

この操作は、適切な位置パラメータを提供することで効率を飛躍的に改善することができる。


## 要件
- (1) : `value_type` は、コンテナに対してコピー挿入可能でなければならない。
- (2) : `value_type` は、コンテナに対してムーブ挿入可能でなければならない。
- (3) : `value_type` は、コンテナに対してコピー挿入可能でなければならない。
- (4) : `value_type` は、コンテナに対してムーブ挿入可能でなければならない。
- (5) : 範囲`[first, last)`の各イテレータが、`*this` の要素を指さないこと。また `value_type` は `*first` から`multimap` コンテナへの`Cpp17EmplaceConstructible`であること。
- (6) : (5)の要件と等価である。
- (7) : `nh` は空である、または、`(*this).get_­allocator() == nh.get_­allocator()`である。
- (8) : `nh` は空である、または、`(*this).get_­allocator() == nh.get_­allocator()`である。

## 効果
- (1) : `x`を`*this`の要素としてコピー挿入する。
- (2) : [`emplace`](emplace.md)`(`[`std::forward`](/reference/utility/forward.md)`<P>(x))`と等価。
- (3) : `x`を`*this`の要素としてコピー挿入する。可能であれば、挿入位置のヒントを表すイテレータ`position`のすぐ前に挿入する。
- (4) : [`emplace_hint`](emplace_hint.md)`(position,` [`std::forward`](/reference/utility/forward.md)`<P>(x))`と等価。
- (5) : 範囲`[first, last)`の各要素を`*this`の要素として挿入する。
- (6) : `insert(init.begin(), init.end())`と等価。
- (7) : `nh`が空の場合、効果はない。そうでなければ、`nh`が所有する要素を挿入し、新しく挿入された要素を指すイテレータを返す。`nh.key()` と等価なキーを持つ要素を含む範囲がコンテナ内に存在する場合、要素はその範囲の終端に挿入される。
- (8) : `nh`が空の場合、効果はなく、`(*this).end()`を返す。そうでなければ、 `nh` によって所有されている要素をコンテナに挿入し、 `nh.key()` と等価なキーを持つ要素を指すイテレータを返す。 `nh.key()` と等しいキーを持つ要素を含む範囲がコンテナ内に存在する場合、要素はその範囲の終端に挿入される。要素は、`p`の直前の位置のできるだけ近くに挿入される。


## 戻り値
- (1), (2), (3), (4) : 新しく挿入された要素を指すイテレータを返す。
- (5), (6) : なし
- (7), (8) : `nh` が空の場合は終端イテレータ、そうでなければ挿入された要素を指すイテレータ。


## 計算量
- (1), (2) : 対数時間。
- (3), (4) : 一般に対数時間だが、`x` が `position` が指す要素の後に挿入された場合は償却定数時間。
- (5), (6) : 一般に N log(size + N) だが、イテレータ範囲`[first, last)` が、コンテナで使われているものと同じ順序基準に従ってソート済みである場合は線形時間。
    - ※ ここで `N` は `first` と `last` の間の距離であり `size` は挿入前のコンテナの [`size()`](size.md)
- (7) : コンテナのサイズの対数、`O(log(size()))`。
- (8) : 挿入が `hint` の直前の位置に行われた場合、償却定数時間。 そうでなければ、コンテナのサイズの対数。

## 備考
(7), (8) の場合、要素はコピーもムーブもされない。

## 例
```cpp example
#include <iostream>
#include <map>

int main ()
{
  std::multimap<char, int> m1;
  std::multimap<char, int> m2;

  m1.insert(std::make_pair('a', 10));
  m1.insert(std::make_pair('b', 20));
  m1.insert(std::make_pair('c', 30));

  std::cout << m1.size() << std::endl;

  m2.insert(m1.begin(), m1.end());
  m2.insert(std::make_pair('d', 40));

  std::cout << m2.size() << std::endl;

  return 0;
}
```
* insert[color ff0000]
* size()[link size.md]
* m1.begin()[link begin.md]
* m1.end()[link end.md]

### 出力
```
3
4
```

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): ??
- [GCC, C++11 mode](/implementation.md#gcc): ??
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): 2012


## 関連項目

| 名前 | 説明 |
|-------------------------------------------------------------------------------------|--------------------------------------|
| [`multimap::erase`](/reference/map/multimap/erase.md) | 要素を削除する |
| [`multimap::find`](/reference/map/multimap/find.md) | 指定したキーで要素を探す |


## 参照
- [N2350 Container insert/erase and iterator constness (Revision 1)](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2350.pdf)
- [N2679 Initializer Lists for Standard Containers(Revision 1)](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2008/n2679.pdf)
- [LWG Issue 2005. `unordered_map::insert(T&&)` protection should apply to `map` too](http://www.open-std.org/jtc1/sc22/wg21/docs/lwg-defects.html#2005)
    - C++14から、(2)と(4)の仕様の書き方が、`unordered_map::insert()`のものと統一された。
- [Splicing Maps and Sets(Revision 5)](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0083r3.pdf)
    - (7), (8)経緯となる提案文書
