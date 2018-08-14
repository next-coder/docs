# 开发规范 —— Objective-C

## 代码规范

此规范包含Objective-C和Swift两部分。

## 一、Objective-c

### 1、间隔及格式化

#### 1）空格或Tabs

代码使用空格进行缩进。在Xcode中，Xcode->Preferences->Text Editing->Indentation设置如下：

Prefer indent using: Spaces

Tab width: 4 spaces

Indent width: 4 spaces

#### 2）代码行宽度

代码行最好不要超过80字符，超过80字符则需要进行适当的换行。tips: 可以在Xcode中设置80字符的临界线(Xcode -> Preferences -> Text Editing -> Page guide at coloumn)。

#### 3）方法的声明与定义

a、-或+后面跟1个空格。

b、参数的类型和参数名之间没有空格。

c、参数声明之间用1个空格隔开。

```Objective-c
- (void)doSomethingWithString:(NSString *)string otherParam:(NSObject *)other {
    // ... 具体代码
}
```

d、如果方法包含多个参数，同时又希望对参数进行换行，则每行使用参数声明前的冒号对齐。如下：

```Objective-c
- (void)doSomethingWithFoo:(GTMFoot *)theFoo
                      rect:(NSRect)theRect
                  interval:(float)theInterval {
    // ... the code
}
```

e、当方法名第一个关键字长度小于后面关键字时，则保留除第一个参数行外的所有参数行按冒号对齐，并且每行至少缩进4个空格。如下：

```Objective-c
- (void)short:(GTMFoo *)theFoo
          longKeyword:(NSRect)theRect
    evenLongerKeyword:(float)theInterval
                error:(NSError **)theError {
  ...
}
```

f、当对方法名进行换行时，每个参数必须单独占一行，并且每行之间不能包含空行。即方法名要么只有一行，要么每个参数一行。

g、禁止使用以下风格：

```Objective-c

// 
- (void)doSomethingWithFoo:(GTMFoot *)theFoo rect:(NSRect)theRect
                  interval:(float)theInterval {
    // ... the code
}

```

#### 4）方法调用

方法调用的代码风格与方法声明类似。

#### 5）@public 和@private都使用一个空格缩进。如：

```Objective-c
@interface MyClass : NSObject {
 @public
 ...
 @private
 ...
}
@end
```

#### 6）异常使用@对齐，每个@新启一行。并且@后的关键字与{或（之间要有空格，异常声明两边要有空格。如：

```Objective-c
@try {
    ...
}
@catch (NSException *ex) {
    ...
}
@finally {
    ...
}
```

#### 7）实现协议的Type<xxx>和protocol类型变量id<xxx>，左<的左侧不能有空格。如：

```Objective-c
@interface MyProtocoledClass : NSObject<NSWindowDelegate> {
 @private
    id<MyFancyDelegate> _delegate;
}
- (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
@end
```

#### 8）Blocks

a、如果Block能够一行写下，则不需要换行。但Block代码开始的{与代码实现之间要有1个空格。

```Objective-c
[operation setCompletionBlock:^{ [self onOperationDone]; }];
```

b、Block内部代码要有4个空格缩进。

```Objective-c
[operation setCompletionBlock:^{
    [self.delegate newDataAvailable];
}];
```

c、如果是多行Block，则Block的结束}必须与Block声明行的第一个字母对齐。

```Objective-c
[[SessionService sharedService]
    loadWindowWithCompletionBlock:^(SessionWindow *window) {
        if (window) {
          [self windowDidLoad:window];
        } else {
          [self errorLoadingWindow];
        }
    }];
```

d、如果Block没有参数，则^和{之间没有空格。如果有参数，则^和(之间没有空格，)和{之间有空格。

```Objective-c
[operation setCompletionBlock:^{ [self onOperationDone]; }];
```

e、调用内嵌Block，Block可以跟调用的label不在同一行，但是要有4个空格缩进。

```Objective-c
[[SessionService sharedService]
    loadWindowWithCompletionBlock:
        ^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];
```

f、Block内部代码过长时，则需要把Block声明成变量，不要使用内嵌Block。

```Objective-c
void (^largeBlock)(void) = ^{
    // ...
};
[_operationQueue addOperationWithBlock:largeBlock];
```

#### 9）容器类的简写法

a、平时尽量用简写法来使用容器。

b、简写法创建容器时，如果只占用一行，则需要再起始括号([、{)后加1个空格，结束括号(]、})前加1个空格。

```Objective-c
NSArray* array = @[ [foo description], @"Another String"  ];
NSDictionary* dict = @{ @"color" : [NSColor redColor] };
```

c、如果简写过长，则需要换行。起始括号要与变量声明在同一行，每个内容单独一行，结束括号单独一行。同时内容要有4个空格缩进。

```Objective-c
NSArray* array = @[
    @"This",
    @"is",
    @"an",
    @"array"
];

NSDictionary* dictionary = @{
    @"font" : [NSFont fontWithName:@"Helvetica-Bold" size:12],
    @"color" : fontColor
};
```

d、对于创建NSDicitonary，key和value中间的冒号左边要有一个空格。右边至少一个空格，如果多个空格，则需要值对齐，不能使用冒号对齐。

```Objective-c
NSDictionary* option1 = @{
  @"font" : [NSFont fontWithName:@"Arial" size:12],
  @"color" : fontColor
};

NSDictionary* option2 = @{
  @"font" :  [NSFont fontWithName:@"Arial" size:12],
  @"color" : fontColor
};
```

### 2、命名

#### 1）源码文件名

a、源码文件名必须与源码的最主要类、协议或功能一致。即如果文件主要是类/协议的声明或实现，则文件名与类名/协议名一样；如果文件不包含主要类/协议/类别，则文件名要突出文件的功能。

b、源码文件中主要是类别，则文件名应当采用‘类名+类别名’的形式。如NSString+Utils.h

### 3、类、协议、类别及其他代码

#### 1）#import

源码文件中的header导入(import)，应当按以下原则分类和排序(从上至下)。如果导入过多时，每个类别之间用空行隔开。

0、与当前文件对应的header，比如类实现中，对应的类声明头文件。

a、系统Framework或lib

b、第三方framework或lib

c、ViewController

d、View

e、Service/Model

f、utils

g、其他

```Objective-c
// this file is named CurrentFileHeader.m
#import "CurrentFileHeader.h"

#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>

#import "HomeViewController.h"
#import "OtherViewController.h"

#import "HomeView.h"
#import "OtherView.h"

#import "HomeService.h"
#import "OtherService.h"
#import "HomeModel.h"
#import "OtherModel.h"

#import "XXUtils.h"
#import "EncryptUtils.h"
#import "KeychainUtils.h"

#import "NSString+Utils.h"
#import "Others.h"
```

#### 2）@class

@class分类和排序与#import遵循一样的规则。@class声明应放在#import之后。

#### 3）类代码

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

a、实例变量必须全部声明为 @private

b、实例变量和属性应该按照变量类型进行分类，同类型变量在同一连续的模块中

c、排序的原则主要根据类型来，自上而下：数据类型(model/int等)、View、ViewController、Others

```Objective-c
@interface MyClass: NSObject<XXX, XXX> {
 @private
    float height;
    NSInteger brothersCount;
    NSString *name;
    NSString *clothBrand;
    MyModel *model;

    UIView *view;
    UIImageView *headImageView;

    UIViewController *viewController;
}

@property (nonatomic) float f1;
@property (nonatomic, strong) NSString *string1;
@property (nonatomic, strong) NSString *string2;
@property (nonatomic, strong) NSString *string3;
@property (nonatomic, strong) MyModel *model;

@property (nonatomic, strong) UIView *view1;
@property (nonatomic, strong) UIButton *button1;

@property (nonatomic, strong) UIViewController *viewController;

@end
```

#### 5）方法

a、实例方法和静态方法的分类和排序都按以下规则。

b、dealloc方法放在最前面，此举为了防止忘记实现dealloc方法，或者在dealloc方法中忘记释放资源。

c、接下来初始化方法，继承父类的初始化方法在前面，其他自定义初始化方法按照参数由少到多进行排序。

d、getter和setter方法，按照对应属性的顺v序排列。

e、提供给外部调用的修改方法。

f、部分类的生命周期方法。按照生命周期调用的先后顺序排序

g、实现协议的方法。需要按照声明中协议的顺序实现。

h、对于私有方法，如果只有另外一个方法在使用，则紧跟在调用方法后面。如果只有一个分类在调用，则写在调用分类的最后面。否则在上面所有分类的后面单独增加一个私有方法分类。

i、其他方法，按功能分类。

j、对于类实现中的每个方法分类，都需要使用#pragma mark - 来进行分段

```Objective-c
@implementation MyClass

- (void)dealloc {
    // ...
}

#pragma mark - init
- (instancetype)init {
    if (self = [super init]) {
        // ...
    }
    return self;
}

- (instancetype)initWithName:(NSString *)name {
    return [self initWithName:name age:0 height:0];
}

- (instancetype)initWithName:(NSString *)name age:(NSInteger)age height:(float)height {
    if (self = [super init]) {
        // ...
    }
    return self;
}

#pragma mark - getter setter
- (void)setName:(NSString *)name {
    //...
}

- (NSString *)name {
    // ...
}

- (void)setCurrentIndex:(NSInteger)index animated:(BOOL)animated {
    // ...
}

// 以下是一个私有方法，用于本块其他方法调用，本块外的方法不能调用
- (void)privateMethod {
    // ...
}

#pragma mark - 生命周期方法
- (void)loadView {
    // ...
}

- (void)viewDidLoad {
    // ...
}

- (void)viewDidAppear:(BOOL)animated {
    // ...
}

#pragma mark - XXXProtocol
- (void)methodInXXXProtocol {
    // ...
}

#pragma mark - AnotherProtocol
- (void)methodInAnotherProtocol {
    // ...
}

#pragma mark - private method
// 私有方法，多个方法分类中有调用
- (void)privateMethodFullScope {
    // ...
}

#pragma mark - static initialize
+ (void)initialize {
    // ...
}

+ (void)load {
    // ...
}

#pragma mark - other static method
+ (instancetype)defaultInstance {
    // ...
}

@end
```

#### 6）协议、类别和扩展也主要遵循以上方式。

#### 7）协议中的optional方法

调用协议的optional方法时，一定要使用respondsToSelector判断调用对象能否响应

#### 8）初始化方法

a、required初始化方法必须实现，并且能够正常使用

b、必须初始化的变量，在Debug模式下需要使用Assert进行校验

c、

### 4、空(nil/Nil/NULL)

#### 1）三个空值的区别与联系

a、三个空值都可以在OC中使用，并且可以相互通用，即三者有同样的语义。只是开发者约定俗成，这三个空值在不同的地方使用，以写出更易阅读的代码。

b、nil用于Objective-c对象。如果变量是OC对象，并且为空值，则使用nil

c、Nil用于Objective-c的Class实例。如果变量类型是Class，并且为空值，则使用Nil

d、NULL用于C/C++语言的代码。

#### 2）空值变量的使用

a、对OC对象的变量，如果调用对象的方法或通过xxx.xxx形式获取属性值时，除特殊情况，一般无需检查是否为空。

b、对OC对象。如果接收的地方需要非空值，则需要对传入变量进行非空检查。

c、针对b，如果在需要非空检查的代码段内，能够保证传入变量值非空，则可以去掉检查。

d、需要接收非空值的地方，主要是向集合类型添加元素、调用需要接收非空参数的函数、代码逻辑需要某个变量值非空等。

e、对于C/C++代码，则需要严格检查非空，即在任何使用的地方都要进行非空检查。

### 5、注释

### 6、其他注意事项

#### 1）方法或函数代码最好不要超过35行

#### 2）文件代码不超过500行