# Universal References (Forwarding References)

在型別宣告時，`&&` 可能代表 `rvalue reference` 或是 `universal reference`。`universal reference` 可能代表 `lvalue reference` 或 `rvalue reference`。

如果該型別是需要推導的，那麼 `&&` 便是 `universal reference`，否則為 `rvalue reference`。

舉例來說，
```cpp
SomeType getSomething();
int getInteger();

auto&& x1 = getSomething();       // universal reference
int&& x2 = getInteger();          // rvalue reference

template<typename T>
void foo(T&& param);              // universal reference

template<typename T>
void foo(std::vector<T>&& param); // rvalue reference

template<typename T>
void foo(const T&& param);        // rvalue reference
```

第一個例子， `auto` 並不知道 `getSomething()` 會回傳怎樣的東西，需要做型別推導，故為 `universal reference`。</br>
第二個例子，一開始就知道型態是 `int`，不需要推導，故為 `rvalue reference`。</br>
第三個例子，並不知道 `T` 是怎樣的型態，需要推導所以是 `universal reference`。</br>
第四個例子，需要推導的 `vector` 內部的型別，`vector` 本身已經是一個已知的型別，故為`rvalue reference`。</br>
第五個例子是特例，如果要推導的型別前面有 `cv-qualified`，視為 `rvalue reference`。

---

`universal reference` 會根據不同的 `expression` 型態來決定轉換成 `rvalue reference` 或是 `lvalue reference`。

 **・使用 `lvalue` 去初始化 `universal reference`，那麼 `universal reference` 便為 `lvalue reference`。</br>
 ・使用 `rvalue` 去初始化 `universal reference`，那麼 `universal reference` 便為 `rvalue reference`**

例如，
```cpp
int getInteger();

int x1 = 5;
auto&& x2 = x1;           // x2 is a lvalue reference
auto&& x3 = 10;           // x3 is a rvalue reference
auto&& x4 = getInteger(); // x4 is a rvalue reference
auto&& x5 = x4;           // x5 is a lvalue reference
```

`x1` 與 `x4` 都是可以被取址的 `lvalue`，所以會產生 `lvalue reference`，而單純的數字與暫時物件都是 `rvalue` 所以會產生 `rvalue reference`。

然而，是怎麼樣的機制讓 `universal reference` 可以轉換成 `rvalue reference` 或是 `lvalue reference` 呢？

## Reference Collapsing

在推導的過程中，`lvalue` 的 `T` 會被視為 `T&`，而 `rvalue`的 `T` 則會被視為 `T`.

舉例來說，
```cpp
template<typename T>
void f(T&& param);

int x;

f(10);  // invoke f on rvalue, call f(int &&)
f(x);   // invoke f on lvalue, call f(int& &&)
```
`f(10)` 會以 `int` 的型態去推導，會得到 `f(int &&)`；`f(x)` 會以 `int&` 的型態去推導，會得到 `f(int& &&)`。

然而，`C++` 中是不允許 `reference-to-reference` 的，所以 `f(x)` 的推導很明顯有問題。這時候就該 `reference collapsing` 上場啦！這條規則會將多餘的 `&` 消去，避免產生不正確的 `reference`。

規則如下，

**・如果是 `rvalue reference` 參照 `rvalue reference`，將其視為 `rvalue reference`。</br>
・其他的參照關係都視為 `lvalue reference`。</br>**

根據規則，`f(int& &&)`就會被變成 `int(int&)`，得到了`lvalue reference`；而 `f(int&&)` 就是 `rvalue reference`。這就是 `universal reference` 如何能轉換成兩種不同 `reference` 的機制。


## 參考資料

1. [Universal References in C++11](https://isocpp.org/blog/2012/11/universal-references-in-c11-scott-meyers)
2. [Forwarding References](https://stackoverflow.com/questions/33904462/whats-the-standard-official-name-for-universal-references)
