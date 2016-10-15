#Masonry [![Build Status](https://travis-ci.org/SnapKit/Masonry.svg?branch=master)](https://travis-ci.org/SnapKit/Masonry) [![Coverage Status](https://img.shields.io/coveralls/SnapKit/Masonry.svg?style=flat-square)](https://coveralls.io/r/SnapKit/Masonry) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

**Masonry is still actively maintained, we are committed to fixing bugs and merging good quality PRs from the wider community. However if you're using Swift in your project, we recommend using [SnapKit](https://github.com/SnapKit/SnapKit) as it provides better type safety with a simpler API.**

-----**Masonry 是一个仍在持续活跃的，我们提交代码去修复bug而且还merge一些高质量的PRs来自更大的社区，无论如何如果你在你的项目中使用 Swift的话，我们推荐你使用 [SnapKit](https://github.com/SnapKit/SnapKit) 就像他提供更好的安全类型with一个更简单的API。 **




Masonry is a light-weight layout framework which wraps AutoLayout with a nicer syntax. Masonry has its own layout DSL which provides a chainable way of describing your NSLayoutConstraints which results in layout code that is more concise and readable.

-----Masonry是个轻量级别的布局框架，利用更好的语法去封装 AutoLayout。 Masonry 拥有自己的DSL(Domain-specific language)布局方式，提供一个链式的方式去描述你的NSLayoutConstraints which 布局你的代码更方便且可读性更强。


Masonry supports iOS and Mac OS X.

-----Masonry 支持 iOS 与 Mac OS X.


For examples take a look at the **Masonry iOS Examples** project in the Masonry workspace. You will need to run `pod install` after downloading.
-----就举个栗子，看看** Masonry iOS Examples ** 项目在 Massory 的工作区。你需要跑 `pod install` 去下载依赖库。


## What's wrong with NSLayoutConstraints?
-----## NSLayoutConstraints 有何问题？

Under the hood Auto Layout is a powerful and flexible way of organising and laying out your views. However creating constraints from code is verbose and not very descriptive.
-----在底层，Auto Layout 是一个强大且灵活的方式去组织布局你的视图。无论如何，使用代码创建一个约束始终都是很啰嗦且可读性很差的。


Imagine a simple example in which you want to have a view fill its superview but inset by 10 pixels on every side
-----想象一个简单的例子，在你有一个想要有一个视图去填充它的父视图，但需要插入每条边10像素的间隙.


```obj-c
UIView *superview = self.view;

UIView *view1 = [[UIView alloc] init];
view1.translatesAutoresizingMaskIntoConstraints = NO;
view1.backgroundColor = [UIColor greenColor];
[superview addSubview:view1];

UIEdgeInsets padding = UIEdgeInsetsMake(10, 10, 10, 10);

[superview addConstraints:@[

    //view1 constraints
    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeTop
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeTop
                                multiplier:1.0
                                  constant:padding.top],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeLeft
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeLeft
                                multiplier:1.0
                                  constant:padding.left],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeBottom
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeBottom
                                multiplier:1.0
                                  constant:-padding.bottom],

    [NSLayoutConstraint constraintWithItem:view1
                                 attribute:NSLayoutAttributeRight
                                 relatedBy:NSLayoutRelationEqual
                                    toItem:superview
                                 attribute:NSLayoutAttributeRight
                                multiplier:1
                                  constant:-padding.right],

 ]];
```
Even with such a simple example the code needed is quite verbose and quickly becomes unreadable when you have more than 2 or 3 views.

-----就算用了如此简单的例子，所需的代码还是有些冗长，而且当你有超过2到3个视图后，代码的可读性就会降低。

Another option is to use Visual Format Language (VFL), which is a bit less long winded.
However the ASCII type syntax has its own pitfalls and its also a bit harder to animate as `NSLayoutConstraint constraintsWithVisualFormat:` returns an array.

-----另一个选择是使用可视化格式语言（VFL），这一点都不啰嗦。
-----无论如何，ASCII类型的语法有他自身的缺陷，而且也有一点难去实现类似'NSLayoutConstraint constraintsWithVisualFormat:' 那样返回一个数组。


## Prepare to meet your Maker!
-----准备迎接你的上帝！


Heres the same constraints created using MASConstraintMaker
-----下面的约束同样都是使用 MASConstraintMaker 所创建的


```obj-c
UIEdgeInsets padding = UIEdgeInsetsMake(10, 10, 10, 10);

[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.top.equalTo(superview.mas_top).with.offset(padding.top); //with is an optional semantic filler
    make.left.equalTo(superview.mas_left).with.offset(padding.left);
    make.bottom.equalTo(superview.mas_bottom).with.offset(-padding.bottom);
    make.right.equalTo(superview.mas_right).with.offset(-padding.right);
}];

```
Or even shorter
-----还可以更简短些


```obj-c
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(superview).with.insets(padding);
}];
```



Also note in the first example we had to add the constraints to the superview `[superview addConstraints:...`.
Masonry however will automagically add constraints to the appropriate view.

-----需要注意在第一个例子中我们已经添加过约束在父视图 `[superview addConstraints:...` 
-----Masonry 无论如何都会自动地添加约束在合适的是视图
                                                                                
                                                                                
                                                                                

Masonry will also call `view1.translatesAutoresizingMaskIntoConstraints = NO;` for you.

-----Masonry 同样会为你调用`view1.translatesAutoresizingMaskIntoConstraints = NO;`

                                                                                
                                                                                
## Not all things are created equal
----- ## 不是所有的创建都使用 equal 

> `.equalTo` equivalent to **NSLayoutRelationEqual**
----- > `.equalTo` 相当于 **NSLayoutRelationEqual**                                                                                

> `.lessThanOrEqualTo` equivalent to **NSLayoutRelationLessThanOrEqual**
----- > `.lessThanOrEqualTo` 相当于 **NSLayoutRelationLessThanOrEqual**


> `.greaterThanOrEqualTo` equivalent to **NSLayoutRelationGreaterThanOrEqual**
----- > `.greaterThanOrEqualTo` 相当于 **NSLayoutRelationGreaterThanOrEqual**

These three equality constraints accept one argument which can be any of the following:
----- 这三个等式约束都可以接受一个参数，参数可以是以下任何一个：
                                                                                

#### 1. MASViewAttribute

```obj-c
make.centerX.lessThanOrEqualTo(view2.mas_left);
```

MASViewAttribute           |  NSLayoutAttribute
-------------------------  |  --------------------------
view.mas_left              |  NSLayoutAttributeLeft
view.mas_right             |  NSLayoutAttributeRight
view.mas_top               |  NSLayoutAttributeTop
view.mas_bottom            |  NSLayoutAttributeBottom
view.mas_leading           |  NSLayoutAttributeLeading
view.mas_trailing          |  NSLayoutAttributeTrailing
view.mas_width             |  NSLayoutAttributeWidth
view.mas_height            |  NSLayoutAttributeHeight
view.mas_centerX           |  NSLayoutAttributeCenterX
view.mas_centerY           |  NSLayoutAttributeCenterY
view.mas_baseline          |  NSLayoutAttributeBaseline

#### 2. UIView/NSView

if you want view.left to be greater than or equal to label.left :
----- 如果你想 view.left 大于或者等于 label.left :
                                                                                
                                                                                
```obj-c
//these two constraints are exactly the same
-----这两个约束实际上是相同的
                                                                                
make.left.greaterThanOrEqualTo(label);
make.left.greaterThanOrEqualTo(label.mas_left);
```

#### 3. NSNumber

Auto Layout allows width and height to be set to constant values.
if you want to set view to have a minimum and maximum width you could pass a number to the equality blocks:

-----Auto Layout 允许宽度和高度设置为常数。
-----如果你想设置视图有一个最大跟最小的宽度，你可以传一个数值到块代码中。
                                                                                
                                                                                
```obj-c
//width >= 200 && width <= 400
make.width.greaterThanOrEqualTo(@200);
make.width.lessThanOrEqualTo(@400)
```

However Auto Layout does not allow alignment attributes such as left, right, centerY etc to be set to constant values.
So if you pass a NSNumber for these attributes Masonry will turn these into constraints relative to the view&rsquo;s superview ie:
                                                                                
-----无论如何 Auto Layout 都不允许对布局属性例如left，right，centerY等去设置为常数值。
-----如果你要用 NSNumber 作为一个属性值来传， Masonry将会将其转换为相对约束来对应父视图，例如：
                                                                                
                                                                                
```obj-c
//creates view.left = view.superview.left + 10
make.left.lessThanOrEqualTo(@10)
```

Instead of using NSNumber, you can use primitives and structs to build your constraints, like so:
```obj-c
make.top.mas_equalTo(42);
make.height.mas_equalTo(20);
make.size.mas_equalTo(CGSizeMake(50, 100));
make.edges.mas_equalTo(UIEdgeInsetsMake(10, 0, 10, 0));
make.left.mas_equalTo(view).mas_offset(UIEdgeInsetsMake(10, 0, 10, 0));
```

By default, macros which support [autoboxing](https://en.wikipedia.org/wiki/Autoboxing#Autoboxing) are prefixed with `mas_`. Unprefixed versions are available by defining `MAS_SHORTHAND_GLOBALS` before importing Masonry.

#### 4. NSArray

An array of a mixture of any of the previous types
```obj-c
make.height.equalTo(@[view1.mas_height, view2.mas_height]);
make.height.equalTo(@[view1, view2]);
make.left.equalTo(@[view1, @100, view3.right]);
````

## Learn to prioritize

> `.priority` allows you to specify an exact priority

> `.priorityHigh` equivalent to **UILayoutPriorityDefaultHigh**

> `.priorityMedium` is half way between high and low

> `.priorityLow` equivalent to **UILayoutPriorityDefaultLow**

Priorities are can be tacked on to the end of a constraint chain like so:
```obj-c
make.left.greaterThanOrEqualTo(label.mas_left).with.priorityLow();

make.top.equalTo(label.mas_top).with.priority(600);
```

## Composition, composition, composition

Masonry also gives you a few convenience methods which create multiple constraints at the same time. These are called MASCompositeConstraints

#### edges

```obj-c
// make top, left, bottom, right equal view2
make.edges.equalTo(view2);

// make top = superview.top + 5, left = superview.left + 10,
//      bottom = superview.bottom - 15, right = superview.right - 20
make.edges.equalTo(superview).insets(UIEdgeInsetsMake(5, 10, 15, 20))
```

#### size

```obj-c
// make width and height greater than or equal to titleLabel
make.size.greaterThanOrEqualTo(titleLabel)

// make width = superview.width + 100, height = superview.height - 50
make.size.equalTo(superview).sizeOffset(CGSizeMake(100, -50))
```

#### center
```obj-c
// make centerX and centerY = button1
make.center.equalTo(button1)

// make centerX = superview.centerX - 5, centerY = superview.centerY + 10
make.center.equalTo(superview).centerOffset(CGPointMake(-5, 10))
```

You can chain view attributes for increased readability:

```obj-c
// All edges but the top should equal those of the superview
make.left.right.and.bottom.equalTo(superview);
make.top.equalTo(otherView);
```

## Hold on for dear life

Sometimes you need modify existing constraints in order to animate or remove/replace constraints.
In Masonry there are a few different approaches to updating constraints.

#### 1. References
You can hold on to a reference of a particular constraint by assigning the result of a constraint make expression to a local variable or a class property.
You could also reference multiple constraints by storing them away in an array.

```obj-c
// in public/private interface
@property (nonatomic, strong) MASConstraint *topConstraint;

...

// when making constraints
[view1 mas_makeConstraints:^(MASConstraintMaker *make) {
    self.topConstraint = make.top.equalTo(superview.mas_top).with.offset(padding.top);
    make.left.equalTo(superview.mas_left).with.offset(padding.left);
}];

...
// then later you can call
[self.topConstraint uninstall];
```

#### 2. mas_updateConstraints
Alternatively if you are only updating the constant value of the constraint you can use the convience method `mas_updateConstraints` instead of `mas_makeConstraints`

```obj-c
// this is Apple's recommended place for adding/updating constraints
// this method can get called multiple times in response to setNeedsUpdateConstraints
// which can be called by UIKit internally or in your code if you need to trigger an update to your constraints
- (void)updateConstraints {
    [self.growingButton mas_updateConstraints:^(MASConstraintMaker *make) {
        make.center.equalTo(self);
        make.width.equalTo(@(self.buttonSize.width)).priorityLow();
        make.height.equalTo(@(self.buttonSize.height)).priorityLow();
        make.width.lessThanOrEqualTo(self);
        make.height.lessThanOrEqualTo(self);
    }];

    //according to apple super should be called at end of method
    [super updateConstraints];
}
```

### 3. mas_remakeConstraints
`mas_updateConstraints` is useful for updating a set of constraints, but doing anything beyond updating constant values can get exhausting. That's where `mas_remakeConstraints` comes in.

`mas_remakeConstraints` is similar to `mas_updateConstraints`, but instead of updating constant values, it will remove all of its constraints before installing them again. This lets you provide different constraints without having to keep around references to ones which you want to remove.

```obj-c
- (void)changeButtonPosition {
    [self.button mas_remakeConstraints:^(MASConstraintMaker *make) {
        make.size.equalTo(self.buttonSize);

        if (topLeft) {
        	make.top.and.left.offset(10);
        } else {
        	make.bottom.and.right.offset(-10);
        }
    }];
}
```

You can find more detailed examples of all three approaches in the **Masonry iOS Examples** project.

## When the ^&*!@ hits the fan!

Laying out your views doesn't always goto plan. So when things literally go pear shaped, you don't want to be looking at console output like this:

```obj-c
Unable to simultaneously satisfy constraints.....blah blah blah....
(
    "<NSLayoutConstraint:0x7189ac0 V:[UILabel:0x7186980(>=5000)]>",
    "<NSAutoresizingMaskLayoutConstraint:0x839ea20 h=--& v=--& V:[MASExampleDebuggingView:0x7186560(416)]>",
    "<NSLayoutConstraint:0x7189c70 UILabel:0x7186980.bottom == MASExampleDebuggingView:0x7186560.bottom - 10>",
    "<NSLayoutConstraint:0x7189560 V:|-(1)-[UILabel:0x7186980]   (Names: '|':MASExampleDebuggingView:0x7186560 )>"
)

Will attempt to recover by breaking constraint
<NSLayoutConstraint:0x7189ac0 V:[UILabel:0x7186980(>=5000)]>
```

Masonry adds a category to NSLayoutConstraint which overrides the default implementation of `- (NSString *)description`.
Now you can give meaningful names to views and constraints, and also easily pick out the constraints created by Masonry.

which means your console output can now look like this:

```obj-c
Unable to simultaneously satisfy constraints......blah blah blah....
(
    "<NSAutoresizingMaskLayoutConstraint:0x8887740 MASExampleDebuggingView:superview.height == 416>",
    "<MASLayoutConstraint:ConstantConstraint UILabel:messageLabel.height >= 5000>",
    "<MASLayoutConstraint:BottomConstraint UILabel:messageLabel.bottom == MASExampleDebuggingView:superview.bottom - 10>",
    "<MASLayoutConstraint:ConflictingConstraint[0] UILabel:messageLabel.top == MASExampleDebuggingView:superview.top + 1>"
)

Will attempt to recover by breaking constraint
<MASLayoutConstraint:ConstantConstraint UILabel:messageLabel.height >= 5000>
```

For an example of how to set this up take a look at the **Masonry iOS Examples** project in the Masonry workspace.

## Where should I create my constraints?

```objc
@implementation DIYCustomView

- (id)init {
    self = [super init];
    if (!self) return nil;

    // --- Create your views here ---
    self.button = [[UIButton alloc] init];

    return self;
}

// tell UIKit that you are using AutoLayout
+ (BOOL)requiresConstraintBasedLayout {
    return YES;
}

// this is Apple's recommended place for adding/updating constraints
- (void)updateConstraints {

    // --- remake/update constraints here
    [self.button remakeConstraints:^(MASConstraintMaker *make) {
        make.width.equalTo(@(self.buttonSize.width));
        make.height.equalTo(@(self.buttonSize.height));
    }];
    
    //according to apple super should be called at end of method
    [super updateConstraints];
}

- (void)didTapButton:(UIButton *)button {
    // --- Do your changes ie change variables that affect your layout etc ---
    self.buttonSize = CGSize(200, 200);

    // tell constraints they need updating
    [self setNeedsUpdateConstraints];
}

@end
```

## Installation
Use the [orsome](http://www.youtube.com/watch?v=YaIZF8uUTtk) [CocoaPods](http://github.com/CocoaPods/CocoaPods).

In your Podfile
>`pod 'Masonry'`

If you want to use masonry without all those pesky 'mas_' prefixes. Add #define MAS_SHORTHAND to your prefix.pch before importing Masonry
>`#define MAS_SHORTHAND`

Get busy Masoning
>`#import "Masonry.h"`

## Code Snippets

Copy the included code snippets to ``~/Library/Developer/Xcode/UserData/CodeSnippets`` to write your masonry blocks at lightning speed!

`mas_make` -> `[<view> mas_makeConstraints:^(MASConstraintMaker *make){<code>}];`

`mas_update` -> `[<view> mas_updateConstraints:^(MASConstraintMaker *make){<code>}];`

`mas_remake` -> `[<view> mas_remakeConstraints:^(MASConstraintMaker *make){<code>}];`

## Features
* Not limited to subset of Auto Layout. Anything NSLayoutConstraint can do, Masonry can do too!
* Great debug support, give your views and constraints meaningful names.
* Constraints read like sentences.
* No crazy macro magic. Masonry won't pollute the global namespace with macros.
* Not string or dictionary based and hence you get compile time checking.

## TODO
* Eye candy
* Mac example project
* More tests and examples

