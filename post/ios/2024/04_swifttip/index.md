# 04_swiftTip

![万字长文详解如何使用Swift提高代码质量](https://img2023.cnblogs.com/blog/2927063/202305/2927063-20230510105716314-83577057.png) 京喜APP最早在2019年引入了Swift，使用Swift完成了第一个订单模块的开发。之后一年多我们持续在团队/公司内部推广和普及Swift，目前Swift已经支撑了70%+以上的业务。通过使用Swift提高了团队内同学的开发效率，同时也带来了质量的提升，目前来自Swift的Crash的占比不到1%。在这过程中不断的学习/实践，团队内的Code Review，也对如何使用Swift来提高代码质量有更深的理解。

# 前言

`京喜APP`最早在2019年引入了`Swift`，使用`Swift`完成了第一个订单模块的开发。之后一年多我们持续在团队/公司内部推广和普及`Swift`，目前`Swift`已经支撑了`70%+`以上的业务。通过使用`Swift`提高了团队内同学的开发效率，同时也带来了质量的提升，目前来自`Swift`的Crash的占比不到`1%`。在这过程中不断的学习/实践，团队内的`Code Review`，也对如何使用`Swift`来提高代码质量有更深的理解。

# Swift特性

在讨论如何使用`Swift`提高代码质量之前，我们先来看看`Swift`本身相比`ObjC`或其他编程语言有什么优势。`Swift`有三个重要的特性分别是`富有表现力`/`安全性`/`快速`，接下来我们分别从这三个特性简单介绍一下：

### 富有表现力

`Swift`提供更多的`编程范式`和`特性`支持，可以编写更少的代码，而且易于阅读和维护。

+   `基础类型` - 元组、Enum`关联类型`
+   `方法` - `方法重载`
+   `protocol` - 不限制只支持`class`、协议`默认`实现、`类`专属协议
+   `泛型` - `protocol`关联类型、`where`实现类型约束、泛型扩展
+   `可选值` - 可选值申明、可选链、隐式可选值
+   `属性` - let、lazy、计算属性\`、willset/didset、Property Wrappers
+   `函数式编程` - 集合`filter/map/reduce`方法，提供更多标准库方法
+   `并发` - async/await、actor
+   `标准库框架` - `Combine`响应式框架、`SwiftUI`申明式UI框架、`Codable`JSON模型转换
+   `Result builder` - 描述实现`DSL`的能力
+   `动态性` - dynamicCallable、dynamicMemberLookup
+   `其他` - 扩展、subscript、操作符重写、嵌套类型、区间
+   `Swift Package Manager` - 基于Swift的包管理工具，可以直接用`Xcode`进行管理更方便
+   `struct` - 初始化方法自动补齐
+   `类型推断` - 通过编译器强大的`类型推断`编写代码时可以减少很多类型申明

> 提示：类型推断同时也会增加一定的编译`耗时`，不过`Swift`团队也在不断的改善编译速度。

### 安全性

#### 代码安全

+   `let属性` - 使用`let`申明常量避免被修改。
+   `值类型` - 值类型可以避免在方法调用等`参数传递`过程中状态被修改。
+   `访问控制` - 通过`public`和`final`限制模块外使用`class`不能被`继承`和`重写`。
+   `强制异常处理` - 方法需要抛出异常时，需要申明为`throw`方法。当调用可能会`throw`异常的方法，需要强制捕获异常避免将异常暴露到上层。
+   `模式匹配` - 通过模式匹配检测`switch`中未处理的case。

#### 类型安全

+   `强制类型转换` - 禁止`隐式类型转换`避免转换中带来的异常问题。同时类型转换不会带来`额外`的运行时消耗。。

> 提示：编写`ObjC`代码时，我们通常会在编码时添加类型检查避免运行时崩溃导致`Crash`。

+   `KeyPath` - `KeyPath`相比使用`字符串`可以提供属性名和类型信息，可以利用编译器检查。
+   `泛型` - 提供`泛型`和协议`关联类型`，可以编写出类型安全的代码。相比`Any`可以更多利用编译时检查发现类型问题。
+   `Enum关联类型` - 通过给特定枚举指定类型避免使用`Any`。

#### 内存安全

+   `空安全` - 通过标识可选值避免`空指针`带来的异常问题
+   `ARC` - 使用`自动`内存管理避免`手动`管理内存带来的各种内存问题
+   `强制初始化` - 变量使用前必须`初始化`
+   `内存独占访问` - 通过编译器检查发现潜在的内存冲突问题

#### 线程安全

+   `值类型` - 更多使用值类型减少在多线程中遇到的`数据竞争`问题
+   `async/await` - 提供`async`函数使我们可以用结构化的方式编写并发操作。避免基于`闭包`的异步方式带来的内存`循环引用`和无法抛出异常的问题
+   `Actor` - 提供`Actor`模型避免多线程开发中进行数据共享时发生的数据竞争问题，同时避免在使用锁时带来的死锁等问题

### 快速

+   `值类型` - 相比`class`不需要额外的`堆内存`分配/释放和更少的内存消耗
+   `方法静态派发` - 方法调用支持`静态`调用相比原有ObjC`消息转发`调用性能更好
+   `编译器优化` - Swift的`静态性`可以使编译器做更多优化。例如`Tree Shaking`相关优化移除未使用的类型/方法等减少二进制文件大小。使用`静态派发`/`方法内联优化`/`泛型特化`/`写时复制`等优化提高运行时性能

> 提示：`ObjC`消息派发会导致编译器无法进行移除无用方法/类的优化，编译器并不知道是否可能被用到。

+   `ARC优化` - 虽然和`ObjC`一样都是使用`ARC`，`Swift`通过编译器优化，可以进行更快的内存回收和更少的内存引用计数管理

> 提示： 相比`ObjC`，Swift内部不需要使用`autorelease`进行管理。

# 代码质量指标

![](https://oscimg.oschina.net/oscnet/up-6f28bb4fcff4d48520c4d0f1ff4f9fc57f2.png)

以上是一些常见的代码质量指标。我们的目标是如何更好的使用`Swift`编写出符合代码质量指标要求的代码。

> 提示：本文不涉及设计模式/架构，更多关注如何通过合理使用`Swift`特性做局部代码段的重构。

## 一些不错的实践

### 利用编译检查

#### 减少使用`Any/AnyObject`

因为`Any/AnyObject`缺少明确的类型信息，编译器无法进行类型检查，会带来一些问题：

+   编译器无法检查类型是否正确保证类型安全
+   代码中大量的`as?`转换
+   类型的缺失导致编译器无法做一些潜在的`编译优化`

使用`as?`带来的问题

当使用`Any/AnyObject`时会频繁使用`as?`进行类型转换。这好像没什么问题因为使用`as?`并不会导致程序`Crash`。不过代码错误至少应该分为两类，一类是程序本身的错误通常会引发Crash，另外一种是业务逻辑错误。使用`as?`只是避免了程序错误`Crash`，但是并不能防止业务逻辑错误。

```swift
func do(data: Any?) {
    guard let string = data as? String else {
        return
    }
    // 
}

do(1)
do("")


```

以上面的例子为例，我们进行了`as?`转换，当`data`为`String`时才会进行处理。但是当`do`方法内`String`类型发生了改变函数，使用方并不知道已变更没有做相应的适配，这时候就会造成业务逻辑的错误。

> 提示：这类错误通常更难发现，这也是我们在一次真实`bug`场景遇到的。

#### 使用`自定义类型`代替`Dictionary`

代码中大量`Dictionary`数据结构会降低代码可维护性，同时带来潜在的`bug`：

+   `key`需要字符串硬编码，编译时无法检查
+   `value`没有类型限制。`修改`时类型无法限制，读取时需要重复类型转换和解包操作
+   无法利用`空安全`特性，指定某个属性必须有值

> 提示：`自定义类型`还有个好处，例如`JSON`转`自定义类型`时会进行`类型/nil/属性名`检查，可以避免将错误数据丢到下一层。

不推荐

```javascript
let dic: [String: Any]
let num = dic["value"] as? Int
dic["name"] = "name"


```

推荐

```dart
struct Data {
  let num: Int
  var name: String?
}
let num = data.num
data.name = "name"


```

适合使用`Dictionary`的场景

+   `数据不使用` - 数据并不`读取`只是用来传递。
+   `解耦` - 1.`组件间通信`解耦使用`HashMap`传递参数进行通信。2.跨技术栈边界的场景，`混合栈间通信/前后端通信`使用`HashMap`/`JSON`进行通信。

#### 使用`枚举关联值`代替`Any`

例如使用枚举改造`NSAttributedString`API，原有API`value`为`Any`类型无法限制特定的类型。

优化前

```go
let string = NSMutableAttributedString()
string.addAttribute(.foregroundColor, value: UIColor.red, range: range)


```

改造后

```csharp
enum NSAttributedStringKey {
  case foregroundColor(UIColor)
}
let string = NSMutableAttributedString()
string.addAttribute(.foregroundColor(UIColor.red), range: range) // 不传递Color会报错


```

#### 使用`泛型`/`协议关联类型`代替`Any`

使用`泛型`或`协议关联类型`代替`Any`，通过`泛型类型约束`来使编译器进行更多的类型检查。

#### 使用`枚举`/`常量`代替`硬编码`

代码中存在重复的`硬编码`字符串/数字，在修改时可能会因为不同步引发`bug`。尽可能减少`硬编码`字符串/数字，使用`枚举`或`常量`代替。

#### 使用`KeyPath`代替`字符串`硬编码

`KeyPath`包含属性名和类型信息，可以避免`硬编码`字符串，同时当属性名或类型改变时编译器会进行检查。

不推荐

```swift
class SomeClass: NSObject {
    @objc dynamic var someProperty: Int
    init(someProperty: Int) {
        self.someProperty = someProperty
    }
}
let object = SomeClass(someProperty: 10)
object.observeValue(forKeyPath: "", of: nil, change: nil, context: nil)


```

推荐

```typescript
let object = SomeClass(someProperty: 10)
object.observe(.someProperty) { object, change in
}


```

### 内存安全

#### 减少使用`!`属性

`!`属性会在读取时隐式`强解包`，当值不存在时产生运行时异常导致Crash。

```kotlin
class ViewController: UIViewController {
    @IBOutlet private var label: UILabel! // @IBOutlet需要使用!
}


```

#### 减少使用`!`进行强解包

使用`!`强解包会在值不存在时产生运行时异常导致Crash。

```dart
var num: Int?
let num2 = num! // 错误


```

> 提示：建议只在小范围的局部代码段使用`!`强解包。

#### 避免使用`try!`进行错误处理

使用`try!`会在方法抛出异常时产生运行时异常导致Crash。

```scss
try! method()


```

#### 使用`weak`/`unowned`避免循环引用

```swift
resource.request().onComplete { [weak self] response in
  guard let self = self else {
    return
  }
  let model = self.updateModel(response)
  self.updateUI(model)
}

resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}


```

#### 减少使用`unowned`

`unowned`在值不存在时会产生运行时异常导致Crash，只有在确定`self`一定会存在时才使用`unowned`。

```less
class Class {
    @objc unowned var object: Object
    @objc weak var object: Object?
}


```

`unowned`/`weak`区别：

+   `weak` - 必须设置为可选值，会进行弱引用处理性能更差。会自动设置为`nil`
+   `unowned` - 可以不设置为可选值，不会进行弱引用处理性能更好。但是不会自动设置为`nil`, 如果`self`已释放会触发错误.

### 错误处理方式

+   `可选值` - 调用方并不关注内部可能会发生错误，当发生错误时返回`nil`
+   `try/catch` - 明确提示调用方需要处理异常，需要实现`Error`协议定义明确的错误类型
+   `assert` - 断言。只能在`Debug`模式下生效
+   `precondition` - 和`assert`类似，可以再`Debug`/`Release`模式下生效
+   `fatalError` - 产生运行时崩溃会导致Crash，应避免使用
+   `Result` - 通常用于`闭包`异步回调返回值

### 减少使用可选值

`可选值`的价值在于通过明确标识值可能会为`nil`并且编译器强制对值进行`nil`判断。但是不应该随意的定义可选值，可选值不能用`let`定义，并且使用时必须进行`解包`操作相对比较繁琐。在代码设计时应考虑`这个值是否有可能为nil`，只在合适的场景使用可选值。

#### 使用`init`注入代替`可选值`属性

不推荐

```typescript
class Object {
  var num: Int?
}
let object = Object()
object.num = 1


```

推荐

```swift
class Object {
  let num: Int

  init(num: Int) {
    self.num = num
  }
}
let object = Object(num: 1)


```

#### 避免随意给予可选值默认值

在使用可选值时，通常我们需要在可选值为`nil`时进行异常处理。有时候我们会通过给予可选值`默认值`的方式来处理。但是这里应考虑在什么场景下可以给予默认值。在不能给予默认值的场景应当及时使用`return`或`抛出异常`，避免错误的值被传递到更多的业务流程。

不推荐

```javascript
func confirmOrder(id: String) {}
// 给予错误的值会导致错误的值被传递到更多的业务流程
confirmOrder(id: orderId ?? "")


```

推荐

```swift
func confirmOrder(id: String) {}

guard let orderId = orderId else {
    // 异常处理
    return
}
confirmOrder(id: orderId)


```

> 提示：通常强业务相关的值不能给予默认值：例如`商品/订单id`或是`价格`。在可以使用兜底逻辑的场景使用默认值，例如`默认文字/文字颜色`。

#### 使用枚举优化可选值

`Object`结构同时只会有一个值存在：

优化前

```kotlin
class Object {
    var name: Int?
    var num: Int?
}


```

优化后

+   `降低内存占用` - `枚举关联类型`的大小取决于最大的关联类型大小
+   `逻辑更清晰` - 使用`enum`相比大量使用`if/else`逻辑更清晰

```java
enum CustomType {
    case name(String)
    case num(Int)
}


```

### 减少`var`属性

#### 使用计算属性

使用`计算属性`可以减少多个变量同步带来的潜在bug。

不推荐

```javascript
class model {
  var data: Object?
  var loaded: Bool
}
model.data = Object()
loaded = false


```

推荐

```kotlin
class model {
  var data: Object?
  var loaded: Bool {
    return data != nil
  }
}
model.data = Object()


```

> 提示：计算属性因为每次都会重复计算，所以计算过程需要轻量避免带来性能问题。

### 控制流

#### 使用`filter/reduce/map`代替`for`循环

使用`filter/reduce/map`可以带来很多好处，包括更少的局部变量，减少模板代码，代码更加清晰，可读性更高。

不推荐

```dart
let nums = [1, 2, 3]
var result = []
for num in nums {
    if num < 3 {
        result.append(String(num))
    }
}
// result = ["1", "2"]


```

推荐

```bash
let nums = [1, 2, 3]
let result = nums.filter { $0 < 3 }.map { String($0) }
// result = ["1", "2"]


```

#### 使用`guard`进行提前返回

推荐

```kotlin
guard !a else {
    return
}
guard !b else {
    return
}
// do


```

不推荐

```less
if a {
    if b {
        // do
    }
}


```

#### 使用三元运算符`?:`

推荐

```bash
let b = true
let a = b ? 1 : 2

let c: Int?
let b = c ?? 1


```

不推荐

```kotlin
var a: Int?
if b {
    a = 1
} else {
    a = 2
}


```

#### 使用`for where`优化循环

`for`循环添加`where`语句，只有当`where`条件满足时才会进入循环

不推荐

```fsharp
for item in collection {
  if item.hasProperty {
    // ...
  }
}


```

推荐

```rust
for item in collection where item.hasProperty {
  // item.hasProperty == true，才会进入循环
}


```

#### 使用`defer`

`defer`可以保证在函数退出前一定会执行。可以使用`defer`中实现退出时一定会执行的操作例如`资源释放`等避免遗漏。

```csharp
func method() {
    lock.lock()
    defer {
        lock.unlock()
        // 会在method作用域结束的时候调用
    }
    // do
}


```

### 字符串

#### 使用`"""`

在定义`复杂`字符串时，使用`多行字符串字面量`可以保持原有字符串的换行符号/引号等特殊字符，不需要使用\`\`进行转义。

```python
let quotation = """
The White Rabbit put on his spectacles.  "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""


```

> 提示：上面字符串中的`""`和换行可以自动保留。

#### 使用字符串插值

使用字符串插值可以提高代码可读性。

不推荐

```vbnet
let multiplier = 3
let message = String(multiplier) + "times 2.5 is" + String((Double(multiplier) * 2.5))


```

推荐

```bash
let multiplier = 3
let message = "(multiplier) times 2.5 is (Double(multiplier) * 2.5)"


```

### 集合

#### 使用标准库提供的高阶函数

不推荐

```csharp
var nums = []
nums.count == 0
nums[0]


```

推荐

```csharp
var nums = []
nums.isEmpty
nums.first


```

### 访问控制

`Swift`中默认访问控制级别为`internal`。编码中应当尽可能减小`属性`/`方法`/`类型`的访问控制级别隐藏内部实现。

> 提示：同时也有利于编译器进行优化。

#### 使用`private`/`fileprivate`修饰私有`属性`和`方法`

```csharp
private let num = 1
class MyClass {
    private var num: Int
}


```

#### 使用`private(set)`修饰外部只读/内部可读写属性

```csharp
class MyClass {
    private(set) var num = 1
}
let num = MyClass().num
MyClass().num = 2 // 会编译报错


```

### 函数

#### 使用参数默认值

使用参数`默认值`，可以使调用方传递`更少`的参数。

不推荐

```swift
func test(a: Int, b: String?, c: Int?) {
}
test(1, nil, nil)


```

推荐

```swift
func test(a: Int, b: String? = nil, c: Int? = nil) {
}
test(1)


```

> 提示：相比`ObjC`，`参数默认值`也可以让我们定义更少的方法。

#### 限制参数数量

当方法参数过多时考虑使用`自定义类型`代替。

不推荐

```swift
func f(a: Int, b: Int, c: Int, d: Int, e: Int, f: Int) {
}


```

推荐

```swift
struct Params {
    let a, b, c, d, e, f: Int
}
func f(params: Params) {
}


```

#### 使用`@discardableResult`

某些方法使用方并不一定会处理返回值，可以考虑添加`@discardableResult`标识提示`Xcode`允许不处理返回值不进行`warning`提示。

```scss
// 上报方法使用方不关心是否成功
func report(id: String) -> Bool {} 

@discardableResult func report2(id: String) -> Bool {}

report("1") // 编译器会警告
report2("1") // 不处理返回值编译器不会警告


```

### 元组

#### 避免过长的元组

元组虽然具有类型信息，但是并不包含`变量名`信息，使用方并不清晰知道变量的含义。所以当元组数量过多时考虑使用`自定义类型`代替。

```x86asm
func test() -> (Int, Int, Int) {

}

let (a, b, c) = test()
// a，b，c类型一致，没有命名信息不清楚每个变量的含义


```

### 系统库

#### `KVO`/`Notification` 使用 `block` API

`block` API的优势：

+   `KVO` 可以支持 `KeyPath`
+   不需要主动移除监听，`observer`释放时自动移除监听

不推荐

```swift
class Object: NSObject {
  init() {
    super.init()
    addObserver(self, forKeyPath: "value", options: .new, context: nil)
    NotificationCenter.default.addObserver(self, selector: #selector(test), name: NSNotification.Name(rawValue: ""), object: nil)
  }

  override class func observeValue(forKeyPath keyPath: String?, of object: Any?, change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
  }

  @objc private func test() {
  }

  deinit {
    removeObserver(self, forKeyPath: "value")
    NotificationCenter.default.removeObserver(self)
  }

}


```

推荐

```typescript
class Object: NSObject {

  private var observer: AnyObserver?
  private var kvoObserver: NSKeyValueObservation?

  init() {
    super.init()
    observer = NotificationCenter.default.addObserver(forName: NSNotification.Name(rawValue: ""), object: nil, queue: nil) { (_) in 
    }
    kvoObserver = foo.observe(.value, options: [.new]) { (foo, change) in
    }
  }
}


```

### Protocol

#### 使用`protocol`代替继承

`Swift`中针对`protocol`提供了很多新特性，例如`默认实现`，`关联类型`，支持值类型。在代码设计时可以优先考虑使用`protocol`来避免臃肿的父类同时更多使用值类型。

> 提示：一些无法用`protocol`替代`继承`的场景：1.需要继承NSObject子类。2.需要调用`super`方法。3.实现`抽象类`的能力。

### Extension

#### 使用`extension`组织代码

使用`extension`将`私有方法`/`父类方法`/`协议方法`等不同功能代码进行分离更加清晰/易维护。

```swift
class MyViewController: UIViewController {
  // class stuff here
}
// MARK: - Private
extension: MyViewController {
    private func method() {}
}
// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // table view data source methods
}
// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // scroll view delegate methods
}


```

### 代码风格

良好的代码风格可以提高代码的`可读性`，统一的代码风格可以降低团队内相互`理解成本`。对于`Swift`的代码`格式化`建议使用自动格式化工具实现，将自动格式化添加到代码提交流程，通过定义Lint`规则`统一团队内代码风格。考虑使用`SwiftFormat`和`SwiftLint`。

> 提示：`SwiftFormat`主要关注代码样式的格式化，`SwiftLint`可以使用`autocorrect`自动修复部分不规范的代码。

常见的自动格式化修正

+   移除多余的`;`
+   最多只保留一行换行
+   自动对齐`空格`
+   限制每行的宽度`自动换行`

## 性能优化

性能优化上主要关注提高`运行时性能`和降低`二进制体积`。需要考虑如何更好的使用`Swift`特性，同时提供更多信息给`编译器`进行优化。

### 使用`Whole Module Optimization`

当`Xcode`开启`WMO`优化时，编译器可以将整个程序编译为一个文件进行更多的优化。例如通过`推断final`/`函数内联`/`泛型特化`更多使用静态派发，并且可以`移除`部分未使用的代码。

### 使用`源代码`打包

当我们使用`组件化`时，为了提高`编译速度`和`打包效率`，通常单个组件独立编译生成`静态库`，最后多个组件直接使用`静态库`进行打包。这种场景下`WMO`仅针对`internal`以内作用域生效，对于`public/open`缺少外部使用信息所以无法进行优化。所以对于大量使用`Swift`的项目，使用`全量代码打包`更有利于编译器做更多优化。

### 减少方法`动态`派发

+   `使用final` - `class`/`方法`/`属性`申明为`final`，编译器可以优化为静态派发
+   `使用private` - `方法`/`属性`申明为`private`，编译器可以优化为静态派发
+   `避免使用dynamic` - `dynamic`会使方法通过ObjC`消息转发`的方式派发
+   `使用WMO` - 编译器可以自动分析推断出`final`优化为静态派发

### 使用`Slice`共享内存优化性能

在使用`Array`/`String`时，可以使用`Slice`切片获取一部分数据。`Slice`保存对原始`Array`/`String`的引用共享内存数据，不需要重新分配空间进行存储。

```erlang
let midpoint = absences.count / 2

let firstHalf = absences[..


```

\`提示：应避免一直持有Slice，Slice会延长原始Array/String的生命周期导致无法被释放造成内存泄漏。

protocol添加AnyObject protocol AnyProtocol {}

protocol ObjectProtocol: AnyObject {}

当protocol仅限制为class使用时，继承AnyObject协议可以使编译器不需要考虑值类型实现，提高运行时性能。

使用@inlinable进行方法内联优化 // 原始代码 let label = UILabel().then {     0.textAlignment\=.center0.textColor = UIColor.black     $0.text = "Hello, World!" }

以then库为例，他使用闭包进行对象初始化以后的相关设置。但是 then 方法以及闭包也会带来额外的性能消耗。

内联优化 @inlinable public func then(\_ block: (Self) throws -> Void) rethrows -> Self { try block(self) return self }

// 编译器内联优化后 let label = UILabel() label.textAlignment = .center label.textColor = UIColor.black label.text = "Hello, World!"

属性 使用lazy延时初始化属性 class View { var lazy label: UILabel = { let label = UILabel() self.addSubView(label) return label }() }

lazy属性初始化会延迟到第一次使用时，常见的使用场景：

初始化比较耗时

可能不会被使用到

初始化过程需要使用self

提示：lazy属性不能保证线程安全

避免使用private let属性

private let属性会增加每个class对象的内存大小。同时会增加包大小，因为需要为属性生成相关的信息。可以考虑使用文件级private let申明或static常量代替。

不推荐 class Object { private let title = "12345" }

推荐 private let title = "12345" class Object { static let title = "" }

提示：这里并不包括通过init初始化注入的属性。

使用didSet/willSet时进行Diff

某些场景需要使用didSet/willSet属性检查器监控属性变化，做一些额外的计算。但是由于didSet/willSet并不会检查新/旧值是否相同，可以考虑添加新/旧值判断，只有当值真的改变时才进行运算提高性能。

优化前 class Object { var orderId: String? { didSet { // 拉取接口等操作 } } }

例如上面的例子，当每一次orderId变更时需要重新拉取当前订单的数据，但是当orderId值一样时，拉取订单数据是无效执行。

优化后 class Object { var orderId: String? { didSet { // 判断新旧值是否相等 guard oldValue != orderId else { return } // 拉取接口等操作 } } }

集合 集合使用lazy延迟序列 var nums = \[1, 2, 3\] var result = nums.lazy.map { String($0) } result\[0\] // 对1进行map操作 result\[1\] // 对2进行map操作

在集合操作时使用lazy，可以将数组运算操作推迟到第一次使用时，避免一次性全部计算。

提示：例如长列表，我们需要创建每个cell对应的视图模型，一次性创建太耗费时间。

使用合适的集合方法优化性能 不推荐 var items = \[1, 2, 3\] items.filter({ $0 > 1 }).first // 查找出所有大于1的元素，之后找出第一个

推荐 var items = \[1, 2, 3\] items.first(where: { $0 > 1 }) // 查找出第一个大于1的元素直接返回

使用值类型

Swift中的值类型主要是结构体/枚举/元组。

启动性能 - APP启动时值类型没有额外的消耗，class有一定额外的消耗。

运行时性能- 值类型不需要在堆上分配空间/额外的引用计数管理。更少的内存占用和更快的性能。

包大小 - 相比class，值类型不需要创建ObjC类对应的ro\_data\_t数据结构。

提示：class即使没有继承NSObject也会生成ro\_data\_t，里面包含了ivars属性信息。如果属性/方法申明为@objc还会生成对应的方法列表。

提示：struct无法代替class的一些场景：1.需要使用继承调用super。2.需要使用引用类型。3.需要使用deinit。4.需要在运行时动态转换一个实例的类型。

提示：不是所有struct都会保存在栈上，部分数据大的struct也会保存在堆上。

集合元素使用值类型

集合元素使用值类型。因为NSArray并不支持值类型，编译器不需要处理可能需要桥接到NSArray的场景，可以移除部分消耗。

纯静态类型避免使用class

当class只包含静态方法/属性时，考虑使用enum代替class，因为class会生成更多的二进制代码。

不推荐 class Object { static var num: Int static func test() {} }

推荐 enum Object { static var num: Int static func test() {} }

提示：为什么用enum而不是struct，因为struct会额外生成init方法。

值类型性能优化 考虑使用引用类型

值类型为了维持值语义，会在每次赋值/参数传递/修改时进行复制。虽然编译器本身会做一些优化，例如写时复制优化，在修改时减少复制频率，但是这仅针对于标准库提供的集合和String结构有效，对于自定义结构需要自己实现。对于参数传递编译器在一些场景会优化为直接传递引用的方式避免复制行为。

但是对于一些数据特别大的结构，同时需要频繁变更修改时也可以考虑使用引用类型实现。

使用inout传递参数减少复制

虽然编译器本身会进行写时复制的优化，但是部分场景编译器无法处理。

不推荐 func append\_one(\_ a: \[Int\]) -> \[Int\] { var a = a a.append(1) // 无法被编译器优化，因为这时候有2个引用持有数组 return a }

var a = \[1, 2, 3\] a = append\_one(a)

推荐

直接使用inout传递参数

func append\_one\_in\_place(a: inout \[Int\]) { a.append(1) }

var a = \[1, 2, 3\] append\_one\_in\_place(&a)

使用isKnownUniquelyReferenced实现写时复制

默认情况下结构体中包含引用类型，在修改时只会重新拷贝引用。但是我们希望CustomData具备值类型的特性，所以当修改时需要重新复制NSMutableData避免复用。但是复制操作本身是耗时操作，我们希望可以减少一些不必要的复制。

优化前 struct CustomData { fileprivate var \_data: NSMutableData var \_dataForWriting: NSMutableData { mutating get { \_data = \_data.mutableCopy() as! NSMutableData return *data } } init(* data: NSData) { self.\_data = data.mutableCopy() as! NSMutableData }

```swift
mutating func append(_ other: MyData) {         _dataForWriting.append(other._data as Data)
}


```

}

var buffer = CustomData(NSData()) for \_ in 0..<5 { buffer.append(x) // 每一次调用都会复制 }

优化后

使用isKnownUniquelyReferenced检查如果是唯一引用不进行复制。

final class Box { var unbox: A init(\_ value: A) { self.unbox = value } }

struct CustomData { fileprivate var \_data: Box var \_dataForWriting: NSMutableData { mutating get { // 检查引用是否唯一 if !isKnownUniquelyReferenced(&\_data) { \_data = Box(\_data.unbox.mutableCopy() as! NSMutableData) } return *data.unbox } } init(* data: NSData) { self.\_data = Box(data.mutableCopy() as! NSMutableData) } }

var buffer = CustomData(NSData()) for \_ in 0..<5 { buffer.append(x) // 只会在第一次调用时进行复制 }

提示：对于ObjC类型isKnownUniquelyReferenced会直接返回false。

减少使用Objc特性 避免使用Objc类型

尽可能避免在Swift中使用NSString/NSArray/NSDictionary等ObjC基础类型。以Dictionary为例，虽然Swift Runtime可以在NSArray和Array之间进行隐式桥接需要O(1)的时间。但是字典当Key和Value既不是类也不是@objc协议时，需要对每个值进行桥接，可能会导致消耗O(n)时间。

减少添加@objc标识

@objc标识虽然不会强制使用消息转发的方式来调用方法/属性，但是他会默认ObjC是可见的会生成和ObjC一样的ro\_data\_t结构。

避免使用@objcMembers

使用@objcMembers修饰的类，默认会为类/属性/方法/扩展都加上@objc标识。

@objcMembers class Object: NSObject { }

提示：你也可以使用@nonobjc取消支持ObjC。

避免继承NSObject

你只需要在需要使用NSObject特性时才需要继承，例如需要实现UITableViewDataSource相关协议。

使用let变量/属性 优化集合创建

集合不需要修改时，使用let修饰，编译器会优化创建集合的性能。例如针对let集合，编译器在创建时可以分配更小的内存大小。

优化逃逸闭包

在Swift中，当捕获var变量时编译器需要生成一个在堆上的Box保存变量用于之后对于变量的读/写，同时需要额外的内存管理操作。如果是let变量，编译器可以保存值复制或引用，避免使用Box。

避免使用大型struct使用class代替

大型struct通常是指属性特别多并且嵌套类型很多。目前swift编译器针对struct等值类型编译优化处理的并不好，会生成大量的assignWithCopy、assignWithCopy等copy相关方法，生成大量的二进制代码。使用class类型可以避免生成相关的copy方法。

提示：不要小看这部分二进制的影响，个人在日常项目中遇到过复杂的大型struct能生成几百KB的二进制代码。但是目前并没有好的方法去发现这类struct去做优化，只能通过相关工具去查看生成的二进制详细信息。希望官方可以早点优化。

优先使用Encodable/Decodable协议代替Codable

因为实现Encodable和Decodable协议的结构，编译器在编译时会自动生成对应的init(from decoder: Decoder)和encode(to: Encoder)方法。Codable同时实现了Encodable和Decodable协议，但是大部分场景下我们只需要encode或decode能力，所以明确指定实现Encodable或Decodable协议可以减少生成对应的方法减少包体积。

提示：对于属性比较多的类型结构会产生很大的二进制代码，有兴趣可以用相关的工具看看生成的二进制文件。

减少使用Equatable协议

因为实现Equatable协议的结构，编译器在编译时会自动生成对应的equal方法。默认实现是针对所有字段进行比较会生成大量的代码。所以当我们不需要实现==比较能力时不要实现Equatable或者对于属性特别多的类型也可以考虑重写Equatable协议，只针对部分属性进行比较，这样可以生成更少的代码减少包体积。

提示：对于属性特别多的类型也可以考虑重写Equatable协议，只针对部分属性进行比较，同时也可以提升性能。

# 总结

个人从Swift3.0开始将Swift作为第一语言使用。编写Swift代码并不只是简单对于ObjC代码的翻译/重写，需要对于Swift特性更多的理解才能更好的利用这些特性带来更多的收益。同时我们需要关注每个版本Swift的优化/改进和新特性。在这过程中也会提高我们的编码能力，加深对于一些通用编程概念/思想的理解，包括空安全、值类型、协程、不共享数据的Actor并发模型、函数式编程、面向协议编程、内存所有权等。对于新的现代编程语言例如Swift/Dart/TS/Kotlin/Rust等，很多特性/思想都是相互借鉴，当我们理解这些概念/思想以后对于理解其他语言也会更容易。

这里推荐有兴趣可以关注Swift Evolution，每个特性加入都会有一个提案，里面会详细介绍动机/使用场景/实现方式/未来方向。

# 扩展链接

The Swift Programming Language

Swift 进阶

SwiftLint Rules

OptimizationTips

深入剖析Swift性能优化

Google Swift Style Guide

Swift Evolution

Dictionary

Array

String

struct\`

标签: [Swift](https://www.cnblogs.com/Jcloud/tag/Swift/) , [代码质量](https://www.cnblogs.com/Jcloud/tag/%E4%BB%A3%E7%A0%81%E8%B4%A8%E9%87%8F/)

[好文要顶](#) [关注我](#) [收藏该文](#) [微信分享](#)

[![](https://pic.cnblogs.com/face/2927063/20230609145847.png)](https://home.cnblogs.com/u/Jcloud/)

[京东云开发者](https://home.cnblogs.com/u/Jcloud/)  
[粉丝 - 375](https://home.cnblogs.com/u/Jcloud/followers/) [关注 - 1](https://home.cnblogs.com/u/Jcloud/followees/)  

[+加关注](#)

0

0

[升级成为会员](https://cnblogs.vip/)

currentDiggType = 0;

[«](https://www.cnblogs.com/Jcloud/p/17387140.html) 上一篇： [从原理到应用，人人都懂的ChatGPT指南](https://www.cnblogs.com/Jcloud/p/17387140.html "发布于 2023-05-10 10:01")  
[»](https://www.cnblogs.com/Jcloud/p/17390152.html) 下一篇： [Webpack5构建性能优化：构建耗时从150s到60s再到10s](https://www.cnblogs.com/Jcloud/p/17390152.html "发布于 2023-05-11 09:52")
