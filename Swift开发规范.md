# 开发规范

## Swift

### 1、间隔及格式化

#### 1）空格或Tabs

代码使用空格或Tab进行缩进。在Xcode中，Xcode->Preferences->Text Editing->Indentation设置如下：

Prefer indent using: Spaces

Tab width: 4 spaces

Indent width: 4 spaces

#### 2）代码行宽度

代码行最好不要超过80字符，超过80字符则需要进行适当的换行。tips: 可以在Xcode中设置80字符的临界线(Xcode -> Preferences -> Text Editing -> Page guide at coloumn)。

#### 3）方法的声明与定义

a、关键字与关键字，关键字与方法名之间用1个空格隔开。

b、参数label与参数名之间用1个空格隔开

c、除第一个参数外，其他参数的参数名或label与逗号之间用一个空格隔开

d、参数名与参数类型声明的冒号之间没有空格，而冒号与参数类型之间用一个空格隔开

```Swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    // your code here
}
```

e、如果方法包含多个参数，同时又希望对参数进行换行，则每行使用label左对齐。如下：

```Swift
override func observeValue(forKeyPath keyPath: String?,
                           of object: Any?,
                           change: [NSKeyValueChangeKey : Any]?,
                           context: UnsafeMutableRawPointer?) {
    // your code here
}
```

f、当对方法名进行换行时，每个参数必须单独占一行，并且每行之间不能包含空行。即方法名要么只有一行，要么每个参数一行。

g、禁止使用以下风格：

```Swift
override func observeValue(forKeyPath keyPath: String?, of object: Any?,
                           change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
    // your code here
}
```

#### 4）方法调用

方法调用的代码风格与方法声明类似。

#### 7）实现协议或继承。

a、如果有继承父类，则继承结构冒号后面第一个必须是父类。

b、继承结构冒号与后面的父类或协议之间有一个空格。但与前面类声明之间没有空格。

c、如果继承结构冒号后面有多个类型，则每个类型与后面的逗号没有空格，与前面的逗号用1个空格隔开

```Swift
class HomeViewController: XNBaseTableViewController, NSObjectProtocol {
    // your code
}
```

#### 8）Closure

a、如果Closure能够一行写下，则不需要换行。但Closure代码开始的{与代码实现之间要有1个空格。

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

b、善用Swift的Inferring Type功能，来简写Closure的参数类型和返回值类型。

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

c、单行表达式的Closure，可以去掉return关键字，Swift默认返回单行语句的执行结果。

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

d、禁止使用参数速记和操作符方法来实现简写Closure

```swift
// 禁用
reversedNames = names.sorted(by: { $0 > $1 } )
// 禁用
reversedNames = names.sorted(by: > )
```

e、如果是多行Closure，则Closure的结束}必须与Closure声明行的第一个字母对齐。

```swift
reversedNames = names.sorted(by: {
    (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

f、Closure内部代码过长时，则需要把Closure声明成变量，不要使用内嵌Closure。

```swift
let closure: (String, String) -> Bool = {
    s1, s2 in
    // your code
}
reversedNames = names.sorted(by: closure )
```

#### 9）容器类的简写法

a、平时尽量用简写法来使用容器。

b、简写法创建容器时，如果只占用一行，则需要再起始括号([)后加1个空格，结束括号(])前加1个空格。

```swift
let array = [ "11111", "22222" ]
let dic = [ "11111": "object", "22222": "object" ]
```

c、如果简写过长，则需要换行。起始括号要与变量声明在同一行，每个内容单独一行，结束括号单独一行。同时内容要有4个空格缩进。

```swift
let array = [
    "This",
    "is",
    "an",
    "array"
]

let dictionary = [
    "font" : "font",
    "color" : "fontColor"
]
```

### 2、命名

#### 1）源码文件名

a、源码文件名必须与源码的最主要类、协议或功能一致。即如果文件主要是类/协议的声明或实现，则文件名与类名/协议名一样；如果文件不包含主要类/协议/类别，则文件名要突出文件的功能。

b、源码文件中主要是类别，则文件名应当采用‘类名+类别名’的形式。如String+Utils.swift

### 3、类、协议、类别及其他代码

#### 1）import

源码文件中的模块导入(import)，应当按以下原则分类和排序(从上至下)。如果导入过多时，每个类别之间用空行隔开。

a、系统Framework或lib

b、第三方framework或lib

c、其他

```swift
import Foundation
import UIKit

import Wechat
import Others
```

#### 2）类代码

类中的代码(类声明、扩展声明、类别及实现)，应按照以下方式进行分类和排序，每个类别之间至少包含一个空行。：

a、@interface或@implementation在类声明的最前面

b、实例变量的声明或@synthesize声明

c、属性声明

d、实例方法声明

e、静态方法声明

f、其他声明

```Objective-c
@interface MyClass: NSObject<XXX, XXX> {
 @private
    float height;
    NSInteger brothersCount;
    NSString *name;
    NSString *clothBrand;
}

@property (nonatomic) float fatherHeight;

- (void)doSomething;

+ (NSArray *)allGenders;

@end
```

#### 4）实例变量和属性

a、实例变量和属性应该按照变量类型进行分类，同类型变量在同一连续的模块中

b、排序的原则主要根据类型来，自上而下：数据类型(model/int等)、View、ViewController、Others

```swift
class MyClass: NSObject {

    let f1: Float
    let string1: String
    var string2: String?
    var string3: String?
    var model: MyModel?

    var view1: UIView?
    var button1: UIButton!

    var viewController: UIViewController
}
```

#### 5）方法

a、实例方法和静态方法的分类和排序都按以下规则。

b、deinit方法放在最前面，此举为了防止忘记实现deinit方法，或者在deinit方法中忘记释放资源。

c、接下来初始化方法，继承父类的初始化方法在前面，其他自定义初始化方法按照参数由少到多进行排序。

e、提供给外部调用的类似getter、setter方法。

f、部分类的生命周期方法。按照生命周期调用的先后顺序排序。

g、实现协议的方法。需要按照声明中协议的顺序实现。

h、对于私有方法，如果只有另外一个方法在使用，则紧跟在调用方法后面。如果只有一个分类在调用，则写在调用分类的最后面。否则在上面所有分类的后面单独增加一个私有方法分类。

i、其他方法，按功能分类。

j、对于类实现中的每个方法分类，都需要使用#pragma mark - 来进行分段

```swift
class MyClass: NSObject {

    var name: String?
    var age: Int?
    var height: Float

    deinit {
        // ...
    }

    // 以下是初始化方法
    init() {
        // ...
        super.init()
    }

    convenience init(name: String) {
        self.init(name: name, age: 0, height: 0)
    }

    init(name: String, age: Int, height: Float) {
        self.name = name
        self.age = age
        self.height = height
    }

    // MARK: 类似的setter方法
    func setCurrentIndex(_ index: Int, animated: Bool) {
        // ...
    }

    // MARK: 以下是一个私有方法，用于本块其他方法调用，本块外的方法不能调用
    func privateMethod() {
        // ...
    }

    // MARK: 生命周期方法
    override func loadView() {
        // ...
    }

    override func viewDidLoad() {
        // ...
    }

    override func viewDidAppear(_ animated: Bool) {
        // ...
    }

    // MARK: XXXProtocol
    func methodInXXXProtocol() {
        // ...
    }

    // MARK: AnotherProtocol
    func methodInAnotherProtocol() {
        // ...
    }

    // MARK: private method
    // 私有方法，多个方法分类中有调用
    func privateMethodFullScope() {
        // ...
    }

    // MARK: static initialize
    static func initialize() {
        // ...
    }

    static func load() {
        // ...
    }

    // MARK: other static method
    static func instance {
        // ...
    }

}
```

#### 6）协议和扩展也主要遵循以上方式。

#### 7）协议中的optional方法

调用协议的optional方法时，一定要使用respondsToSelector判断调用对象能否响应

### 4、空值

#### 1）尽可能的使用普通(非optional)变量

#### 2）对于optional变量，善用optional chain、 if let、 guard let等

#### 3）optional变量中，!类型的使用条件如下：

a、在使用前一定初始化过的变量中

b、在类初始化方法中初始化为非空值，并且类声明周期结束前一直非空的成员变量

c、LoadNib来实现的ViewController或View中的视图成员变量

#### 4）在OC和Swift混编的时候，对于接收!类型参数的方法，一定确定方法能处理空值，否则必须传入非空值

### 5、注释

#### 1）特殊处理、特殊参数、复杂逻辑等必须添加注释

#### 2）尽可能的多写注释，注释必须明确标出功能、使用、注意事项等

#### 3）类注释和方法注释应采用多行注释

```swift
/*
    功能：

    使用：

    注意事项：

    其他：
*/
class MyClass: NSObject {
    /*
        功能：
            ...
            
            @param paramname comment
            @param paramname comment
            @param paramname comment

            @return type comment

        使用：

        注意事项：

        其他：
    */
    func myMethod() {

    }
}
```

#### 4）简短的语句注释采用单行注释，较长的语句注释采用多行注释

```swift
func myMethod() {
    // ...
    doSomething()

    /*
        ...
    */
    doOtherThings() {

    }
}
```

### 6、其他注意事项

#### 1）方法或函数代码最好不要超过35行

#### 2）文件代码不超过500行
