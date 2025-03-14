---
~
---

这个笔记意图容纳想写 unsafe 代码应当知道的东西。

参考资料：
- [Rust Reference](https://rustwiki.org/zh-CN/reference/type-layout.html)
- [Programing Rust](https://github.com/MeouSker77/ProgrammingRust)

## 类型布局

### 尺寸和对齐量

所有值都有一个对齐量和尺寸。对齐量为 n 的值只能存储地址为 n 的倍数的内存地址上。对齐量至少是 1，总是 2 的倍数。值的对齐量可以使用 `align_of_val` 来检查。

而值的尺寸，或者是值的大小是指值所占用的字节数，这也包括为了对齐而所做的填充量。可以用 `size_of_val` 来检查。

如果某类型的所有值都具有相同的尺寸和对齐量，且都是编译时已知的，并且是 `Sized` 的，则可以使用 `size_of` 和 `align_of` 来进行检查。用法如 `size_of::<T>()`。


### 原生类型的布局

| 类型              | `size_of::<Type>()` |
| --------------- | ------------------- |
| `bool`          | 1                   |
| `u8` / `i8`     | 1                   |
| `u16` / `i16`   | 2                   |
| `u32` / `i32`   | 4                   |
| `u64` / `i64`   | 8                   |
| `u128` / `i128` | 16                  |
| `f32`           | 4                   |
| `f64`           | 8                   |
| `char`          | 4                   |

`usize` 和 `isize` 的尺寸跟平台的内存地址有关，在 32 位上是 4 bytes，而 64 位是 8 bytes。

大多数原生类型的对齐量和它们的尺寸是一致的，但是这也跟平台有关。

### 指针和引用的布局

- 指针和引用具有相同的布局，且跟可变性无关。
- 指向 sized type 的指针和 usize 具有相同布局。
- 指向 unsized type 的指针是固定尺寸的，其尺寸和对齐量至少等于一个指针的尺寸和对齐量。目前指向 DST 的指针都是两倍的 usize 的尺寸。

### 数组的布局

数组 `[T;n]` 的布局的尺寸为 `size_of::<T>() * n`，对齐量和 T 相同。

### 切片的布局

切片的布局和它们切的数组片段的布局相同，不要把指向切片的指针（`&[T], Box<[T]>`）和切片混淆，这里所说的是原生 `[T]` 类型。

### 元组的布局

不保证元组的布局，`()` 除外，尺寸为 0，对齐量为 1。

### Trait 对象的布局

Trait 对象的布局和其的值相同。

### 闭包的布局

不保证闭包的布局。

## `representation` 属性

该属性用于指定复合类型的布局，可能有：
- `#[repr(default)]`，rust 默认的表形，不保证每次编译有统一的布局。
- `#[repr(C)]`，C 的表型。
- `#[repr(<primitive>)]`，如 `#[repr(u8)]`，只能用于枚举。
- `#[repr(transparent)]`，让单字段枚举的布局和它所包含字段的布局相同。

可以使用 `#[repr(align)]` 和 `#[repr(packed)]` 分别增大和减小结构体和联合体的对齐量。`#[repr(packed)]` 也可以改变字段之间的填充。

## Ptr 

`Ptr` 中的方法都是对指针常用操作的封装。

在使用 `Ptr` 中的函数时，一定要保证参数中的指针是“有效”的。关于指针的有效性至少保证以下几点：

- 对于任意零字节的访问，任何指针都是有效的，包括 null 指针。以下其余几点只考虑**非零大小**的访问。
- Null 指针总是无效的。


## `NonNull<T>`

`NonNull<T>` 是用于包裹裸指针的函数，它仅仅只是表示一个不能为空的指针。
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
