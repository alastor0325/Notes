# Type Trait

`type trait` 是一種用於支援泛型編程中將不同型態的特殊資訊封裝起來的一種方法，而在 `C++11` 中
更提供了標準的函式庫 [`<type_traits>`](https://en.cppreference.com/w/cpp/header/type_traits)。

讓我們來假設以下情境，

我們現在有一個類型 `Test`，我希望它能夠針對傳入的參數型態做不同的處理。如果傳入參數是指標的話，
`Test` 就會有一些額外的實作。

實踐這件事情最簡單的方式就是額外傳入一個參數，用來標明傳入的參數是否為指標。

```cpp
template <typename T, bool isPointer>
class Test {
   // Implementation might be changed if T is a pointer
}
```
但這樣的實作方式太冗了，每次都要多傳一個參數，如果有好幾個條件，不就要傳一堆參數？

於是我們可以考慮以下的寫法，自己定義一個新的類型把資訊封裝起來。

```cpp
template<typename T>
struct PointerTrait
{
  static const bool isPointer = false;
};

template<typename T>
struct PointerTrait<T*>
{
  static const bool isPointer = true;
};
```
於是我們就可以利用 `PointerTrait` 來簡單的判斷是否傳入參數為指標。

```cpp
if (PointerTrait<T>::isPointer) {
  // Is pointer
} else {
  // Not a pointer
}
```
