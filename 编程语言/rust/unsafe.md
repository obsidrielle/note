---
~
---

这个笔记意图容纳想写 unsafe 代码应当知道的东西。

## 类型布局



## `Nonnull<T>`

`nonnull<T>` 是用于包裹裸指针的函数，它仅仅只是表示一个不能为空的指针。
其不拥有包裹指针指向的元素的**所有权**，因此需要手动地析构。
如果需要可空的指针，使用 `Option<NonNull<T>>`。


### 构造方法

| 方法              | 函数签名                                                     | 描述                                                | 示例                                                        | 注意事项              |
| --------------- | -------------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------------- | ----------------- |
| `new`           | `pub fn new(ptr: *mut T) -> Option<NonNull<T>>`          | 从裸指针创建 `NonNull<T>`，若指针非空则返回 `Some`               | `let ptr: *mut i32 = ...;<br>let nn = NonNull::new(ptr);` | 检查指针是否为 `null`    |
| `new_unchecked` | `pub unsafe fn new_unchecked(ptr: *mut T) -> NonNull<T>` | 直接包装裸指针为 `NonNull<T>`，不检查非空性                      | `unsafe {<br> let nn = NonNull::new_unchecked(ptr);<br>}` | **必须确保 `ptr` 非空** |
| `dangling`      | `pub const fn dangling() -> NonNull<T>`                  | 创建一个 dangling 但是对齐的指针，常用于需要懒初始化的情景，如 `Vec::new()` |                                                           |                   |
### 指针操作

| 方法       | 函数签名                                               | 描述                 | 示例                                           | 注意事项               |
| -------- | -------------------------------------------------- | ------------------ | -------------------------------------------- | ------------------ |
| `as_ptr` | `pub fn as_ptr(self) -> *mut T`                    | 返回裸指针 `*mut T`     | `let raw_ptr: *mut T = nn.as_ptr();`         | 不转移所有权             |
| `as_ref` | `pub unsafe fn as_ref<'a>(&self) -> &'a T`         | 将指针转换为共享引用         | `unsafe {<br> let value = nn.as_ref();<br>}` | **需确保指针有效且生命周期合法** |
| `as_mut` | `pub unsafe fn as_mut<'a>(&mut self) -> &'a mut T` | 将指针转换为可变引用         | `unsafe {<br> let value = nn.as_mut();<br>}` | **需确保独占访问权**       |
| `cast`   | `pub fn cast<U>(self) -> NonNull<U>`               | 改变指针类型（类似 `as` 转换） | `let nn_u8: NonNull<u8> = nn.cast::<u8>();`  | 类型需兼容内存布局          |
### 类型转换

| 方法               | 函数签名                                                                                         | 描述                 | 示例                | 注意事项                |
| ---------------- | -------------------------------------------------------------------------------------------- | ------------------ | ----------------- | ------------------- |
| `from_raw_parts` | `pub fn from_raw_parts(data: NonNull<()>, metadata: <T as Pointee>::Metadata) -> NonNull<T>` | 从原始指针部分构造（适用于动态类型） | 用于 `dyn Trait` 场景 | 高级用法，需理解元数据         |
| `to_raw_parts`   | `pub fn to_raw_parts(self) -> (NonNull<()>, <T as Pointee>::Metadata)`                       | 分解为原始指针部分          | 用于动态类型解析          | 需配合 `Pointee` trait |
|                  |                                                                                              |                    |                   |                     |
### 其他实用方法

| 方法              | 函数签名                                                                   | 描述                      | 示例       | 注意事项    |
| --------------- | ---------------------------------------------------------------------- | ----------------------- | -------- | ------- |
| `as_uninit_ref` | `pub unsafe fn as_uninit_ref<'a>(&self) -> &'a MaybeUninit<T>`         | 转换为 `MaybeUninit` 的共享引用 | 处理未初始化数据 | 需手动初始化  |
| `as_uninit_mut` | `pub unsafe fn as_uninit_mut<'a>(&mut self) -> &'a mut MaybeUninit<T>` | 转换为 `MaybeUninit` 的可变引用 | 写入未初始化内存 | 需确保内存可写 |

## Ptr 

`Ptr` 中的方法都是对指针常用操作的封装。

在使用 `Ptr` 中的函数时，一定要保证参数中的指针是“有效”的。关于指针的有效性至少保证以下几点：

- 对于任意零字节的访问，任何指针都是有效的，包括 null 指针。以下其余几点只考虑**非零大小**的访问。
- Null 指针总是无效的。
- 