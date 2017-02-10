# Writing Test Classes and Methods

When you add a test target to a project with the test navigator, Xcode displays the test classes and methods from that target in the test navigator. In the test target are the test classes containing test methods. This chapter explains how you create test classes and write test methods.

## Test Targets, Test Bundles, and the Test Navigator

Before looking at creating test classes, it is worth taking another look at the test navigator. Using it is central to creating and working with tests.

Adding a test target to a project creates a test bundle. The test navigator lays out the source code components of all test bundles in the project, displaying the test classes and test methods in a hierarchical list. Here’s a test navigator view for a project that has two test targets, showing the nested hierarchy of test bundles, test classes, and test methods.


Test bundles can contain multiple test classes. You can use test classes to segregate tests into related groups, either for functional or organizational purposes. For example, for the calculator example project you might create BasicFunctionsTests, AdvancedFunctionsTests, and DisplayTests classes, all part of the Mac_Calc_Tests test bundle.

Some types of tests might share certain types of setup and teardown requirements, making it sensible to gather those tests together into classes, where a single set of setup and teardown methods can minimize how much code you have to write for each test method.

## Creating a Test Class

Note: This chapter focuses on unit test classes and methods for purposes of illustration. Creating UI test targets, classes, and methods, and how that differs from working with unit tests, is discussed in User Interface Testing.
You use the Add button (+) in the test navigator to create new test classes.

You can choose to add either a Unit Test Class or a UI Test Class. After choosing one of these, Xcode displays a file type selector that has the chosen type of file template selected. A “New Unit Test Class” template is highlighted in the illustration below. Click Next to proceed with your selection.

Each test class you add results in a file named TestClassName.m being added to the project, based on the test class name you enter in the configuration sheet.

Note: All test classes are subclasses of XCTestCase, provided by the XCTest framework.

Although by default Xcode organizes test class implementation files into the group it created for your project’s test targets, you can organize the files in your project however you choose. The standard Xcode Add Files sheet follows this configuration when you press the Next button.

You use the Add Files sheet the same way as when adding new files to the project in the project navigator. For details on how to use the Add Files sheet, see Adding an Existing File or Folder.

Note: When you create a new project, a test target and associated test bundle are created for you by default with names derived from the name of your project. For instance, creating a new project named MyApp automatically generates a test bundle named MyAppTests and a test class named MyAppTests with the associated MyAppTests.m implementation file.

## Test Class Structure

Test classes have this basic structure:

```Objective-C

#import <XCTest/XCTest.h>
 
@interface SampleCalcTests : XCTestCase
@end
 
@implementation SampleCalcTests
 
- (void)setUp {
    [super setUp];
    // Put setup code here. This method is called before the invocation of each test method in the class.
}
 
- (void)tearDown {
    // Put teardown code here. This method is called after the invocation of each test method in the class.
    [super tearDown];
}
 
- (void)testExample {
    // This is an example of a functional test case.
    // Use XCTAssert and related functions to verify your tests produce the correct results.
}
 
- (void)testPerformanceExample {
    // This is an example of a performance test case.
    [self measureBlock:^{
        // Put the code you want to measure the time of here.
    }];
}
@end

```

The test class is implemented in Objective-C in this example, but can also be implemented in Swift.

Note: The implementation examples in this text are all written in Objective-C for consistency.
Swift is fully compatible with using XCTest and implementing your test methods. All Swift and Objective-C cross-language implementation capabilities can be used as well.

Notice that the implementation contains methods for instance setup and teardown with a basic implementation; these methods are not required. If all of the test methods in a class require the same code, you can customize setUp and tearDown to include it. The code you add runs before and after each test method runs. You can optionally add customized methods for class setup (+ (void)setUp) and teardown (+ (void)tearDown) as well, which run before and after all of the test methods in the class.

## Writing Test Methods

You add tests to a test class by writing test methods. A test method is an instance method of a test class that begins with the prefix test, takes no parameters, and returns void, for example, (void)testColorIsRed(). A test method exercises code in your project and, if that code does not produce the expected result, reports failures using a set of assertion APIs. For example, a function’s return value might be compared against an expected value or your test might assert that improper use of a method in one of your classes throws an exception. XCTest Assertions describes these assertions.

For a test method to access the code to be tested, import the corresponding header files into your test class.

When Xcode runs tests, it invokes each test method independently. Therefore, each method must prepare and clean up any auxiliary variables, structures, and objects it needs to interact with the subject API. If this code is common to all test methods in the class, you can add it to the required setUp and tearDown instance methods described in Test Class Structure.

Here is the model of a unit test method:

```Objective-C
- (void)testColorIsRed {
   // Set up, call test subject API. (Code could be shared in setUp method.)
   // Test logic and values, assertions report pass/fail to testing framework.
   // Tear down. (Code could be shared in tearDown method.
}
```

And here is a simple test method example that checks to see whether the CalcView instance was successfully created for SampleCalc, the app shown in the Quick Start chapter:

```Objective-C
- (void) testCalcView {
   // setup
   app = [NSApplication sharedApplication];
   calcViewController = (CalcViewController*)[NSApplication sharedApplication] delegate];
   calcView             = calcViewController.view;
 
   XCTAssertNotNil(calcView, @"Cannot find CalcView instance");
   // no teardown needed
}
```

## Writing Tests of Asynchronous Operations

Tests execute synchronously because each test is invoked independently one after another. But more and more code executes asychronously. To handle testing components which call asynchronously executing methods and functions, XCTest has been enhanced in Xcode 6 to include the ability to serialize asynchronous execution in the test method, by waiting for the completion of an asynchronous callback or timeout.

A source example:

```Objective-C
// Test that the document is opened. Because opening is asynchronous,
// use XCTestCase's asynchronous APIs to wait until the document has
// finished opening.
- (void)testDocumentOpening
{
    // Create an expectation object.
    // This test only has one, but it's possible to wait on multiple expectations.
    XCTestExpectation *documentOpenExpectation = [self expectationWithDescription:@"document open"];
 
    NSURL *URL = [[NSBundle bundleForClass:[self class]]
                              URLForResource:@"TestDocument" withExtension:@"mydoc"];
    UIDocument *doc = [[UIDocument alloc] initWithFileURL:URL];
    [doc openWithCompletionHandler:^(BOOL success) {
        XCTAssert(success);
        // Possibly assert other things here about the document after it has opened...
 
        // Fulfill the expectation-this will cause -waitForExpectation
        // to invoke its completion handler and then return.
        [documentOpenExpectation fulfill];
    }];
 
    // The test will pause here, running the run loop, until the timeout is hit
    // or all expectations are fulfilled.
    [self waitForExpectationsWithTimeout:1 handler:^(NSError *error) {
        [doc closeWithCompletionHandler:nil];
    }];
}

```
For more details on writing methods for asynchronous operations, see the XCTestCase+AsynchronousTesting.h header file in XCTest.framework.


## Writing Performance Tests

A performance test takes a block of code that you want to evaluate and runs it ten times, collecting the average execution time and the standard deviation for the runs. The averaging of these individual measurements form a value for the test run that can then be compared against a baseline to evaluate success or failure.

Note: The baseline is a value that you have specified to be used for the evaluation of test pass or failure. The report UI provides a mechanism to set or change the baseline value.
To implement performance measuring tests, you write methods using new API from XCTest in Xcode 6 and later.


```Objective-C
- (void)testPerformanceExample {
    // This is an example of a performance test case.
    [self measureBlock:^{
        // Put the code you want to measure the time of here.
    }];
}

```

The following simple example shows a performance test written to test addition speed with the calculator sample app. A measureBlock: is added along with an iteration for XCTest to time.

```Objective-C
- (void) testAdditionPerformance {
    [self measureBlock:^{
        // set the initial state
        [calcViewController press:[calcView viewWithTag: 6]];  // 6
        // iterate for 100000 cycles of adding 2
        for (int i=0; i<100000; i++) {
           [calcViewController press:[calcView viewWithTag:13]];  // +
           [calcViewController press:[calcView viewWithTag: 2]];  // 2
           [calcViewController press:[calcView viewWithTag:12]];  // =
        }
    }];
}

```

Performance tests, once run, provide information in the source editor when viewing the implementation file, in the test navigator, and in the reports navigator. Clicking on the information presents individual run values. The results display includes controls to set the results as the baseline for future runs of the tests. Baselines are stored per-device-configuration, so you can have the same test executing on several different devices and have each maintain a different baseline dependent upon the specific configuration’s processor speed, memory, and so forth.

Note: Performance measuring tests always report failure on the first run and until a baseline value is set on a particular device configuration.
For more details on writing methods for performance measuring tests, see the XCTestCase.h header file in XCTest.framework.

##Writing UI Tests

Creating UI tests with XCTest is an extension of the same programming model as creating unit tests. Similar operations and programming methodology is used overall. The differences in workflow and implementation are focused around using UI recording and the XCTest UI testing APIs, described in User Interface Testing.

## Writing Tests with Swift

The Swift access control model prevents tests from accessing internal declarations in the app or framework. To access internal functions in Swift with Xcode 6, you need to make these entry points public for testing, reducing the benefits of Swift’s type safety.

Xcode 7 provides a two-part solution for this problem:

Swift internals are made accessible when building for testing by informing the compiler of the build’s intent. The changes to compilation are controlled via a new -enable-testing flag, emitted during the build when Xcode executes your test build action. This behavior is controlled by the Enable Testability build setting, set to Yes by default for new projects; it requires no changes to your source code.
The visibility of testable entry points is restricted to clients who ask for it via a modification to import statements. The modification is implemented by a new @testable attribute to import, a one-time change to your test code that requires no changes to your app code.
For example, consider a Swift module like this AppDelegate implementation for an app named “MySwiftApp.”


```Objective-C
import Cocoa
@NSApplicationMain
class AppDelegate: NSObject, NSApplicationDelegate {
    @IBOutlet weak var window: NSWindow!
    func foo() {
        println("Hello, World!")
    }
}
```

To write a test class that allows access to the AppDelegate class, you modify the import statement in your test code with the @testable attribute, as follows:

```Objective-C
// Importing XCTest because of XCTestCase
import XCTest
 
// Importing AppKit because of NSApplication
import AppKit
 
// Importing MySwiftApp because of AppDelegate
@testable import MySwiftApp
 
class MySwiftAppTests: XCTestCase {
    func testExample() {
        let appDelegate = NSApplication.sharedApplication().delegate as! AppDelegate
        appDelegate.foo()
    }
}
```


With this solution in place, your Swift app code’s internal functions are fully accessible to your test classes and methods. The access granted for @testable imports ensures that other, non-testing clients do not violate Swift’s access control rules even when compiling for testing.

Note: @testable provides access only for “internal” functions; “private” declarations are not visible outside of their file even when using @testable.


## XCTest Assertions

Your test methods use assertions provided by the XCTest framework to present test results that Xcode displays. All assertions have a similar form: items to compare or a logical expression, a failure result string format, and the parameters to insert into the string format.

Note: The last parameter of all assertions is format..., a format string and its variable parameter list. XCTest provides a default failure result string for all assertions, assembled using the parameters passed to the assertion. The format string offers the ability to provide an additional, custom description of the failure that you can choose to supply in addition to the provided description. This parameter is optional and can be omitted.
For example, look at this assertion in the testAddition method presented in Quick Start:

XCTAssertEqualObjects([calcViewController.displayField stringValue], @"8", @"Part 1 failed.");

Reading this as plain language, it says “Indicate a failure when a string created from the value of the controller’s display field is not the same as the reference string ‘8’.” Xcode signals with a fail indicator in the test navigator if the assertion fails, and Xcode also displays a failure with the description string in the issues navigator, source editor, and other places. A typical result in the source editor looks like this:

A test method can include multiple assertions. Xcode signals a test method failure if any of the assertions it contains reports a failure.

Assertions fall into five categories: unconditional fail, equality tests, nil tests, Boolean tests, and exception tests. For example:

###Unconditional Fail
XCTFail. Generates a failure unconditionally

```Objective-C
XCTFail(format...)
```

###Equality Tests
XCTAssertEqual. Generates a failure when expression1 is not equal to expression2. This test is for scalars.

```Objective-C
XCTAssertEqual(expression1, expression2, format...)
```
See Assertions Listed by Category for documentation on the full set of XCTest assertions.

##Using Assertions with Objective-C and Swift

When using XCTest assertions, you should know the fundamental differences in the assertions’ compatibility and behavior when writing Objective-C (and other C-based languages) code and when writing Swift code. Understanding these differences makes writing and debugging your tests easier.

XCTest assertions that perform equality tests are divided between those that compare objects and those that compare nonobjects. For example, XCTAssertEqualObjects tests equality between two expressions that resolve to an object type. And XCTAssertEqual tests equality between two expressions that resolve to the value of a scalar type. This difference is marked in the XCTest assertions listing by including “this test is for scalars” in the description. Marking assertions with “scalar” this way informs you of the basic distinction, but it is not an exact description of which expression types are compatible.

*For Objective-C, assertions marked for scalar types can be used with the types that can be used with the equality comparison operators: ==, !=, <=, <, >=, and >. If the expression resolves to any C type, struct, or array comparison that works with these operators, it is considered a scalar.

*For Swift, assertions marked for scalars can be used to compare any expression type that conforms to the Equatable protocol (for all of the “equal” and “not equal” assertions) and Comparable protocol (for the “greater than” and “less than” assertions). In addition, assertions marked for scalars have overrides for [T] and for [K:V], where T, K, and V conform to Equatable or Comparable protocols. For example, arrays of an equatable type are compatible with XCTAssertEqual, and dictionaries whose keys and values are both comparable types are compatible with XCTAssertLessThan.


Note: In Swift, NSObject conforms to Equatable, so using XCTAssertEqualObjects works but isn’t necessary.

Using XCTest assertions in your tests also differs between Objective-C and Swift because of how the languages differ in treating data types and implicit conversions.

*For Objective-C, the use of implicit conversions in the XCTest implementation allows the comparisons to operate independent of the expressions’ data types, and no check is made of the input data types.

*For Swift, implicit conversions are not allowed because Swift is stricter about type safety; both parameters to a comparison must be of the same type. Type mismatches are flagged at compile time and in the source editor.

## Assertions Listed by Category
XCTestAssertions are categorized into five groups. ... Unconditional Fail, Equality Tests, Nil Tests, Boolean

The following sections list the XCTest assertions. You can obtain more information on XCTest assertions by referencing XCTestAssertions.h in Xcode using Quick Help.

### Unconditional Fail
XCTFail. Generates a failure unconditionally

```Objective-C
XCTFail(format...)
```

Equality Tests
XCTAssertEqualObjects. Generates a failure when expression1 is not equal to expression2 (or one object is nil and the other is not).

XCTAssertEqualObjects(expression1, expression2, format...)
XCTAssertNotEqualObjects. Generates a failure when expression1 is equal to expression2.

XCTAssertNotEqualObjects(expression1, expression2, format...)
XCTAssertEqual. Generates a failure when expression1 is not equal to expression2. This test is for scalars.

XCTAssertEqual(expression1, expression2, format...)
XCTAssertNotEqual. Generates a failure when expression1 is equal to expression2. This test is for scalars.

XCTAssertNotEqual(expression1, expression2, format...)
XCTAssertEqualWithAccuracy. Generates a failure when the difference between expression1 and expression2 is greater than accuracy. This test is for scalars such as floats and doubles, where small differences could make these items not exactly equal, but works for all scalars.

XCTAssertEqualWithAccuracy(expression1, expression2, accuracy, format...)
XCTAssertNotEqualWithAccuracy. Generates a failure when the difference between expression1 and expression2 is less than or equal to accuracy. This test is for scalars such as floats and doubles, where small differences could make these items not exactly equal, but works for all scalars.

XCTAssertNotEqualWithAccuracy(expression1, expression2, accuracy, format...)
XCTAssertGreaterThan. Generates a failure when expression1 is less than or equal to expression2. This test is for scalar values.

XCTAssertGreaterThan(expression1, expression2, format...)
XCTAssertGreaterThanOrEqual. Generates a failure when expression1 is less than expression2. This test is for scalar values.

XCTAssertGreaterThanOrEqual(expression1, expression2, format...)
XCTAssertLessThan. Generates a failure when expression1 is greater than or equal to expression2. This test is for scalar values.

XCTAssertLessThan(expression1, expression2, format...)
XCTAssertLessThanOrEqual. Generates a failure when expression1 is greater than expression2. This test is for scalar values.

XCTAssertLessThanOrEqual(expression1, expression2, format...)
Nil Tests
XCTAssertNil. Generates a failure when the expression parameter is not nil.

XCTAssertNil(expression, format...)
XCTAssertNotNil. Generates a failure when the expression parameter is nil.

XCTAssertNotNil(expression, format...)
Boolean Tests
XCTAssertTrue. Generates a failure when expression evaluates to false.

XCTAssertTrue(expression, format...)
XCTAssert. Generates a failure when expression evaluates to false. Synonymous with XCTAssertTrue.

XCTAssert(expression, format...)
XCTAssertFalse. Generates a failure when expression evaluates to true.

XCTAssertFalse(expression, format...)
Exception Tests
XCTAssertThrows. Generates a failure when expression does not throw an exception.

XCTAssertThrows(expression, format...)
XCTAssertThrowsSpecific. Generates a failure when expression does not throw an exception of a specific class.

XCTAssertThrowsSpecific(expression, exception_class, format...)
XCTAssertThrowsSpecificNamed. Generates a failure when expression does not throw an exception of a specific class with a specific name. Useful for those frameworks like AppKit or Foundation that throw generic NSException with specific names (NSInvalidArgumentException and so forth).

XCTAssertThrowsSpecificNamed(expression, exception_class, exception_name, format...)
XCTAssertNoThrow. Generates a failure when an expression does throw an exception.

XCTAssertNoThrow(expression, format...)
XCTAssertNoThrowSpecific. Generates a failure when expression does throw an exception of the specified class. Any other exception is OK; that is, it does not generate a failure.

XCTAssertNoThrowSpecific(expression, exception_class, format...)
XCTAssertNoThrowSpecificNamed. Generates a failure when expression does throw an exception of a specific class with a specific name. Useful for those frameworks like AppKit or Foundation that throw generic NSException with specific names (NSInvalidArgumentException and so forth).

XCTAssertNoThrowSpecificNamed(expression, exception_class, exception_name, format...)

