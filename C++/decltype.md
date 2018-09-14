# Decltype

為 `C++11` 的新標準，可查詢表達式的類型，在此關鍵字出現前，通常使用編譯器的擴展關鍵字 `typeof`。

用法為 `Decltype ( expression | entity)`, 詳細規格請點
[此](https://en.cppreference.com/w/cpp/language/decltype)

例如：
```cpp
int a1;
float a2();
struct A {
  double val;
};
A a3;

decltype(a1) x1;      // int
decltype(a2) x2;      // float
decltype(a3.val) x3;  // double
decltype((a3.val)) x4 // double&
```

---

而 `decltype` 的用法可以分成以下幾種，

#### 1. 推導類型
```cpp
int a;
decltype(a) b;
```
#### 2. 定義類型 (與 `using` 或 `typedef` 連用)
```cpp
using nullptr_t = decltype(nullptr);
```
#### 3. 推導泛型函數返回值

舉例來說，以下代碼中我們並不清楚傳入參數的型態，所以我們沒有辦法知道該填寫怎樣的型態給回傳值。
```cpp
template<typename A, typename B>
UNKNOWN foo(A a, B b)
{
  return a + b;
}
```

使用 `decltype` 後代碼會長得像這樣，便可推斷出返回值的型態。
```cpp
template<typename A, typename B>
decltype((*(A*)0) + (*(B*)0)) foo(A a, B b)
{
  return a + b;
}
```

但如果搭配上`auto`，整個寫法就更加優美了。
```cpp
template<typename A, typename B>
auto foo(A a, B b) -> decltype(a + b)
{
  return a + b;
}
```
