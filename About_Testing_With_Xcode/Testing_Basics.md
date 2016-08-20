# Testing Basics
# 测试理论

A test is code you write that exercises your app and library code and results in a pass or fail result, measured against a set of expectations. A test might check the state of an object’s instance variables after performing some operations, verify that your code throws a particular exception when subjected to boundary conditions, and so forth. For a performance measuring test, the reference standard could be a maximum amount of time within which you expect a set of routines to run to completion.

测试是对你所写的代码的一种锻炼，通过对测试结果的通过与否，得出一系列的期望值。测试应当在进行一些特定的操作后验证目标的实例变量的状态，当到达边界值时验证你的代码是否抛出特定的异常，等等。易于性能测量的测试，参考标准可能是在一个最大的限定时间内，跑完一系列你期望的例程(routines)。 
[routines这里不太好翻，最后一句大概意思是理解，但是不太好翻]


## Defining Test Scope
## 定义测试范围

All software is built using composition; that is, smaller components are arranged together to form larger, higher-level components with greater functionality until the goals and requirements of the project are met. Good testing practice is to have tests that cover functionality at all levels of this composition. XCTest allows you to write tests for components at any level.

所有的程序构建都需要使用composition;也就是说，通过把许多小的组件组合起来，形成一个更大型、更高级的组件并具有更强大的功能，直到项目的目标与要求得到满足。好的测试实践，是需在这个composition中覆盖到所有的不同层级中的功能。
[composition这里需要翻译出来吗？最后一句好像也不太顺]


It’s up to you to define what constitutes a component for testing—it could be a method in a class or a set of methods that accomplish an essential purpose. For example, it could be an arithmetic operation, as in the calculator app used as an example in the Quick Start chapter. It could be the different methods that handle the interaction between the contents of a TableView and a list of names you maintain in your code’s data structures. Each one of those methods and operations implies a component of the app’s functionality and a test to check it. The behavior of a component for testing should be completely deterministic; the test either passes or fails.
这取决于你去定义一个组件是适合构成去测试构成一个雷或者一些列方法去完成一系列的可能。举个例子，它可能回事个算法操作，就像计算器app的快速开始教程中就像一个例子。他可能是不同的方法去控制交互在tableView里的内容，和一列名字你维持在你的代码数据结构。每一个那种方法和操作暗示一个app功能的组件和一个测试去检查他。这种组件的行为去测试应该完全确定；这个测试是通过还是失败。
[调整下语序，先直译了后面再调整]



The more you can divide up the behavior of your app into components, the more effectively you can test that the behavior of your code meets the reference standards in all particulars as your project grows and changes. For a large project with many components, you’ll need to run a large number of tests to test the project thoroughly. Tests should be designed to run quickly, when possible, but some tests are necessarily large and execute more slowly. Small, fast running tests can be run often and used when there is a failure in order to help diagnose and fix problems easily.
你更加可以把你的app的各种行为分为组件，更加有效你可以测试那些关你的代码的行为参考标准在所有详情迹象你的项目成长和变化。当一个项目有许多的组件，你需要运行大量的Tests用例去彻底地测试你的项目。Tests应该设计成跑的很快，当尽可能，但有时有些测试是不必须很大型还有执行起来很慢。小快，跑起来很快的tests可以经常跑并有用，当这是有一个错误的指令，去帮助去分析与修复问题更加容易

[the more那个该怎么翻译好]



Tests designed for the components of a project are the basis of test-driven development, which is a style of writing code in which you write test logic before writing the code to be tested. This development approach lets you codify requirements and edge cases for your code before you implement it. After writing the tests, you develop your algorithms with the aim of passing the tests. After your code passes the tests, you have a foundation upon which you can make improvements to your code, with confidence that any changes to the expected behavior (which would result in bugs in your product) are identified the next time you run the tests.
为项目组件而设计的测试是测试驱动开发的基础，which is是一种写代码的风格in which 在你写的代买被测试之前你要写你的测试逻辑。这种开发的方式让你制定需求和你的代码在边缘的情况下在你的代码实现之前。写了测试用例之后，你以一个目标去通过你的测试去开发你的算法。当你的代码通过测试，你的代码质量会有一个基础的提升，有信心对语气的行为有任何的改变（这将导致你的产品会有bug）是保证确定下次运行在你的测试。
[result in---导致

]


Even when you’re not using test-driven development, tests can help reduce the introduction of bugs in your code as you modify it to enhance features and functionality. You incorporate testing in a working app to ensure that future changes don’t modify the app’s existing behavior other than in your planned ways. As you fix bugs, you add tests that confirm that the bugs are fixed. Tests should exercise your code, looking for both expected successes and expected failures, to cover all the boundary conditions.
即使你不适用“测试驱动开发”的方式，测试仍然可以帮助你减少当你先修改你的代码时的bug的引入，提高功能性与实用性。你融入测试在你使用中的app去确保功能的变化不会去修改你的app现有的行为除了在你的计划中。就像你修复你的bug，你添加测试去确定bug是已经被修复的。测试可以锻炼你的代码，寻找语气的成功与失败，去覆盖所有的边界条件。
[other than 除了]



Note: Adding tests to a project that was not designed with testing in mind may require redesigning or refactoring parts of the code to make it easier to test them. Appendix A: Writing Testable Code contains simple guidelines for writing testable code that you might find useful.
Note：添加测试项目，没有设计测试概念可能需要重新设计或重构部分的代码更容易测试。附录A：编写可测试代码包含编写可测试代码的简单指南，你可能会发现有用的代码。

Components can encompass the interactions between any of the various parts of your app. Because some types of tests take much longer to run, you might want to run them only periodically or only on a server. As you’ll see in the next chapters, you can organize your tests and run them in many different ways to meet different needs.

组件可以包尾所有的监护在你的app所有变化部分之间。因为一些类型的测试需要花更长的时间去跑，你可能想让他们只在服务器上定时地去跑。在你接下来看到的下一个章节，你可以组织你你的测试还有运行他们通过不同的方式去看到不同的需要。


## Performance Testing
## 执行测试

Tests of components can be either functional in nature or measure performance. XCTest provides API to measure time-based performance, enabling you to track performance improvements and regressions in a similar way to functional compliance and regressions.
测试组件可以不是功能性测试就是性能的检测。XCTest提供API去确保基于时间的性能，确保你去跟踪性能的提高与功能的回归在一个相似的方式去改进与回归。



To provide a success or failure result when measuring performance, a test must have a baseline to evaluate against. A baseline is a combination of the average time performance in ten runs of the test method with a measure of the standard deviation of each run. Tests that drop below the time baseline or that vary too much from run to run are reported as failures.
提供一个成功或者失败的结果当在测试性能的时候，一个测试比较有一个基线去做针对性评估。基线是平均时间性能的组合在十次运行的测试方法中，用每一次运行的标准偏差去测量每一次运行。当测试结果低于基准线太多或者变化太多时候，测试报告都会表示为失败。


Note: The first time you run a performance measurement test, XCTest always reports failure since the baseline is unknown. Once you have accepted a certain measurement as a baseline, XCTest evaluates and reports success or failure, and provides you with a means to see the results of the test in detail.
Note: 第一次运行性能测试的时候，XCTest总是报告失败，因为基准线都是未知的。一旦你确定了测量的基准线，XCTest评促与报告成功与否，还会提供一个方法去查看详细的测试结果。

[once 一旦]


##User Interface Testing
##用户界面的测试


The functional and performance testing discussed so far is generally referred to as unit testing, where a “unit” is the component of functionality that you have decided upon with respect to granularity and level. Unit testing is primarily concerned with forming good components that behave as expected and interact with other components as expected. From the design perspective, unit testing approaches your development project from its inside, vetting that the components fulfill your intent.
功能与性能的测试的讨论就到目前未知，通常这些都被成为单元测试，其中一个“单元”是指功能的组成部分，你已经决定的颗粒度与水平。单元测试是根本上关系到怎么组成一个好的组件，如期房的行为与交互达到预期。从测试角度来看，单元测试从其内部开发项目的方法，审核，部分实现你的意图。
[so far 到此为止]





Users interact with these internals of your code through the user interface. User interface interactions are generally coarser-grained, higher level behaviors, taking external activity and integrating the operation of several components (subsystems) to call your app’s functions. It is difficult to write unit tests to exercise the interactions that users experience through the user interface without special facilities designed to operate the UI from outside the app context. Tests written with these special facilities are called “UI tests.”
用户通过UI来与这些内部的代码进行交互。UI的相互作用，通畅是粗粒的，高等级的行为，取得外部的活动与集成一些组件（子系统）去调用你的app的功能。

[user interface就是用户界面，即UI]




UI tests approach testing apps from the external surface, as the users experience it. They enable you to write tests that send simulated events to both system-supplied and custom UI objects, capture the response of those objects, and then test that response for correctness or performance much like you do with the internally-oriented unit tests.
UI测试方法是通过外部表面去测试你的app，就像用户去体验它，他们可以让你去写测试去发送模拟事件给系统提供的和自定义UI对象，捕捉这些对象的响应，再测试这些响应的正确性与性能，就像你做的内部导向的单元测试。



##App and Library Tests
##应用与库的测试


Xcode offers two types of unit test contexts: app tests and library tests.

Xcode提供两种类型的单元测试环境：应用测试与库测试。




App tests. App tests check the correct behavior of code in your app, such as the example of the calculator app’s arithmetic operations.
应用测试，应用测试检查你应用中的代码的正确行为，就像例子中你的计算器app的算法操作。



Library tests. Library tests check the correct behavior of code in dynamic libraries and frameworks independent of their use in an app’s runtime. With library tests you construct unit tests that exercise the components of a library.
库测试。库的测试是检查代码在动态库中的正确行为，在应用的runtime运行时独立使用的框架。



Testing your projects using these test contexts as appropriate helps you maintain expected and anticipated behavior as your code evolves over time.
测试你的项目时适当地使这些环境可以帮助你位置所期望的效果随着你的代码的演变与时间推移。



## XCTest—the Xcode Testing Framework
## XCTest—Xcode 的测试框架

XCTest is the testing framework supplied for your use, starting with Xcode 5.

XCTest是一个提供给你使用的测试框架，在Xcode5开始使用。


Regarding versions and compatibility:

In Xcode 5, XCTest is compatible with running on OS X v10.8 and OS X v10.9, and with iOS 7 and later.
In Xcode 6, XCTest is compatible with running on OS X v10.9 and OS X v10.10, and with iOS 6 and later.
In Xcode 7, XCTest is compatible with running on OS X v10.10 and OS X v10.11, and with iOS 6 and later.
UI tests are supported running on OS X v10.11 and iOS 9, both in Simulator and on devices.

For more detailed version compatibility information, see Xcode Release Notes.

Xcode incorporates XCTest.framework into your project. This framework provides the APIs that let you design tests and run them on your code. For detailed information about the XCTest framework, see XCTest Framework Reference.

Note: Xcode includes a migrator for updating projects that have existing OCUnit tests. For more information about migrating OCUnit to XCTest, see Appendix B: Transitioning from OCUnit to XCTest.


## Where to Start When Testing
## 何时开始测试


When you start to create tests, keep the following ideas in mind:
当你开始创建测试，保持以下观念在你的脑海里：



When creating unit tests, focus on testing the most basic foundations of your code, the Model classes and methods, which interact with the Controller.
当创建一个单元测试，，重点在于测试你代码的最基础的功能，Model的类与方法，



A high-level block diagram of your app most likely has Model, View, and Controller classes—it is a familiar design pattern to anyone who has been working with Cocoa and Cocoa Touch. As you write tests to cover all of your Model classes, you’ll have the certainty of knowing that the base of your app is well tested before you work your way up to writing tests for the Controller classes—which start to touch other more complex parts of your app, for example, a connection to the network with a database connected behind a web service.

As an alternative starting point, if you are authoring a framework or library, you may want to start with the surface of your API. From there, you can work your way in to the internal classes.

When creating UI tests, start by considering the most common workflows. Think of what the user does when getting started using the app and what UI is exercised immediately in that process. Using the UI recording feature is a great way to capture a sequence of user actions into a UI test method that can be expanded upon to implement tests for correctness and/or performance.
UI tests of this type tend to start with a relatively coarse-grained focus and might cut across several subsystems; they can return a lot of information that can be hard to analyze at first. As you work with your UI test suite, you can refine the testing granularity and focus UI tests to reflect specific subsystem behaviors more clearly.
