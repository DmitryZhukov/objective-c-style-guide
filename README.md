## Objective-C Style Guide

### Table of Contents

* [Общее](#oбщее)
* [Example](#example)
* [Language](#language)
* [Code Organization](#code-organization)
* [Spacing](#spacing)
* [Comments](#comments)
* [Naming](#naming)
* [Underscores](#underscores)
* [Methods](#methods)
* [Variables](#variables)
* [Property Attributes](#property-attributes)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Literals](#literals)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Case Statements](#case-statements)
* [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Conditionals](#conditionals)
* [Ternary Operator](#ternary-operator)
* [Init Methods](#init-methods)
* [Class Constructor Methods](#class-constructor-methods)
* [CGRect Functions](#cgrect-functions)
* [Golden Path](#golden-path)
* [Error handling](#error-handling)
* [Singletons](#singletons)
* [Line Breaks](#line-breaks)
* [Xcode Project](#xcode-project)


## Oбщее

* В коде не допускается оставлять закомментированный код. Если данный код, возможно, потребуется в будущем, можно закоммитить его, а следующим коммитом удалить. 
* Ошибки должны передаваться через объект **NSError**. Атрибут словаря **localizedDescription** должен быть заполнен, так как текст для сообщения пользователю должен получаться из этого атрибута.
* Обсудить стиль и формат логирования, в том числе сам логгер (возможно надо привести в порядок). Уровни логирования (warning, debug ...)
* Именование методов в делегате. Именование самих делегатов. (взять от [Apple](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-1001803-BCIDAIJE)) 
* Документирование кода (Классов, публичных методов, переменных и свойств) и реализацию, если имеет сложные особенности работы.
* В документации к методу указывать в каком потоке будет происходить вызов callback (по умолчанию в главном потоке).
* Использовать обязательно архитектуру [MVVM](https://www.objc.io/issues/13-architecture/mvvm/)(взаимодействие на делегатах). [MVVM-C](https://www.youtube.com/watch?v=9VojuJpUuE8) (С - координатор, отвечает за презентацию и сокрытие нужных представлений, обладает минимальной логикой решения куда направить поток представлений).
* При необходимости в коде желательно оставлять метки **TODO**. Формат 
```objectivec
	//TODO: <описание>
```
* Файлы исходного кода должны быть разделены логически по физическим папкам в проекте. Позволит разобрать свалку исходников, при необходимости можно будет легко брать куски логики и, например, использовать в другом проекте. По возможности текущие исходники разделять по логике.
* В коде не допустимо использование прямого использования названия идентификатора ячейки, контроллера, перехода и тд. Даже если оно используется один раз. (static const *)
* Использование макросов необходимо объединять в одном месте, не разбрасывать по функциям. Лучше объявить все константы в одном define в начале кода, чем в каждом методе. С осторожностью (по возможности вообще не использовать) вложенные define, особенно, когда ветвится большой кусок кода.
* UI нового функционала нужно разбивать на отдельные сториборды, связи между сторибордами организовывать при помощи Storyboard References.

[Back to Table of Contents ](#table-of-contents)
## Example 

They say an example is worth a thousand words, so let's start off with an
example that should give you a feel for the style, spacing, naming, and so on.

Here is an example header file, demonstrating the correct commenting and spacing
for an `@interface` declaration.

**Preferred:**
```objectivec 
#import <Foundation/Foundation.h>

@class Bar;

/**
 * A sample class demonstrating good Objective-C style. All interfaces,
 * categories, and protocols (read: all non-trivial top-level declarations
 * in a header) MUST be commented. Comments must also be adjacent to the
 * object they're documenting.
 */
@interface Foo : NSObject

/** The retained Bar. */
@property (nonatomic, strong) Bar *bar;

/** The current drawing attributes. */
@property (nonatomic, copy) NSDictionary<NSString *, NSNumber *> *attributes;

/**
 * Convenience creation method.
 * See -initWithBar: for details about @c bar.
 *
 * @param bar The string for fooing.
 * @return An instance of Foo.
 */
+ (instancetype)fooWithBar:(Bar *)bar;

/**
 * Designated initializer.
 *
 * @param bar A string that represents a thing that does a thing.
 */
- (instancetype)initWithBar:(Bar *)bar;

/**
 * Does some work with @c blah.
 *
 * @param blah
 * @return YES if the work was completed; NO otherwise.
 */
- (BOOL)doWorkWithBlah:(NSString *)blah;

@end
```

An example source file, demonstrating the correct commenting and spacing for the
`@implementation` of an interface.

```objectivec 
#import "Shared/Util/Foo.h"

@interface Foo () {
  /** The string used for displaying "hi". */
  NSString *_string;
}
@end

@implementation Foo 

+ (instancetype)fooWithBar:(Bar *)bar 
{
  return [[self alloc] initWithBar:bar];
}

- (instancetype)init 
{
  // Classes with a custom designated initializer should always override
  // the superclass's designated initializer.
  return [self initWithBar:nil];
}

- (instancetype)initWithBar:(Bar *)bar
{
  self = [super init];
  if (self) {
    _bar = [bar copy];
    _string = [[NSString alloc] initWithFormat:@"hi %d", 3];
    _attributes = @{
      @"color" : [UIColor blueColor],
      @"hidden" : @NO
    };
  }
  return self;
}

- (BOOL)doWorkWithBlah:(NSString *)blah
{
  // Work should be done here.
  return NO;
}

@end
```
## Introduction

The reason we made this style guide was so that we could keep the code in our books, tutorials, and starter kits nice and consistent - even though we have many different authors working on the books.

This style guide is different from other Objective-C style guides you may see, because the focus is centered on readability for print and the web. Many of the decisions were made with an eye toward conserving space for print, easy legibility, and tutorial writing.

## Principles

### Optimize for the reader, not the writer

Codebases often have extended lifetimes and more time is spent reading the code
than writing it. We explicitly choose to optimize for the experience of our
average software engineer reading, maintaining, and debugging code in our
codebase rather than the ease of writing said code. For example, when something
surprising or unusual is happening in a snippet of code, leaving textual hints
for the reader is valuable.

### Be consistent

When the style guide allows multiple options it is preferable to pick one option
over mixed usage of multiple options. Using one style consistently throughout a
codebase lets engineers focus on other (more important) issues. Consistency also
enables better automation because consistent code allows more efficient
development and operation of tools that format or refactor code. In many cases,
rules that are attributed to "Be Consistent" boil down to "Just pick one and
stop worrying about it"; the potential value of allowing flexibility on these
points is outweighed by the cost of having people argue over them.

### Be consistent with Apple SDKs

Consistency with the way Apple SDKs use Objective-C has value for the same
reasons as consistency within our code base. If an Objective-C feature solves a
problem that's an argument for using it. However, sometimes language features
and idioms are flawed, or were just designed with assumptions that are not
universal. In those cases it is appropriate to constrain or ban language
features or idioms.

### Style rules should pull their weight

The benefit of a style rule must be large enough to justify asking engineers to
remember it. The benefit is measured relative to the codebase we would get
without the rule, so a rule against a very harmful practice may still have a
small benefit if people are unlikely to do it anyway. This principle mostly
explains the rules we don’t have, rather than the rules we do: for example, goto
contravenes many of the following principles, but is not discussed due to its
extreme rarity.

## Background

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html)
* [Mac App Programming Guide](https://developer.apple.com/library/content/documentation/General/Conceptual/MOSXAppProgrammingGuide/Introduction/Introduction.html)
* [App Programming Guide for watchOS](https://developer.apple.com/library/content/documentation/General/Conceptual/WatchKitProgrammingGuide/)
* [App Programming Guide for tvOS](https://developer.apple.com/library/content/documentation/General/Conceptual/AppleTV_PG/)
* [View Controller Programming Guide for iOS](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/)

[Back to Table of Contents ](#table-of-contents)
## Language

US English should be used.

**Preferred:**
```objectivec
UIColor *myColor = [UIColor whiteColor];
```

**Not Preferred:**
```objectivec
UIColor *myColour = [UIColor whiteColor];
```

[Back to Table of Contents ](#table-of-contents)
## Code Organization

Use `#pragma mark -` to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

```objectivec
#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```
[Back to Table of Contents ](#table-of-contents)
## Spacing

* Indent using 4 spaces (this conserves space in print and makes line wrapping less likely). Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces always open on a new line and close on a new line.
* Other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**Preferred:**
```objectivec
- (NSString *)methodName
{
    if (user.isHappy) {
		//Do something
	} else {
		//Do something else
	}
}
```

**Not Preferred:**
```objectivec
- (NSString *)methodName {
	if (user.isHappy) {
	    //Do something
	}
	else
	    //Do something else
}
```

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.
* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

**Preferred:**

```objectivec
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
  // something
} completion:^(BOOL finished) {
  // something
}];
```

**Not Preferred:**

```objectivec
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```
[Back to Table of Contents ](#table-of-contents)
## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. *Exception: This does not apply to those comments used to generate documentation.*

[Back to Table of Contents ](#table-of-contents)
## Naming

[Apple naming conventions](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF) should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Preferred:**

```objectivec
UIButton *settingsButton;
```

**Not Preferred:**

```objectivec
UIButton *setBut;
```

One to three letter prefix should always be used for class names and constants, however may be omitted for Core Data entity names. For Money Pro the prefix '**MP**' should be used.

Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**Preferred:**

```objectivec
static NSTimeInterval const MPViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not Preferred:**

```objectivec
static NSTimeInterval const fadetime = 1.7;
```

Properties should be camel-case with the leading word being lowercase. Use auto-synthesis for properties rather than manual @synthesize statements unless you have good reason.

**Preferred:**

```objectivec
@property (nonatomic, strong) NSString *descriptiveVariableName;
```

**Not Preferred:**

```objectivec
id varnm;
```

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

Local variables should not contain underscores.

[Back to Table of Contents ](#table-of-contents)
## Methods

In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objectivec
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objectivec
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

[Back to Table of Contents ](#table-of-contents)
## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

[Private properties](#private-properties) should be used in place of instance variables whenever possible. Although using instance variables is a valid way of doing things, by agreeing to prefer properties our code will be more consistent. 

Direct access to instance variables that 'back' properties should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**Preferred:**

```objectivec
@interface MPTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
```

**Not Preferred:**

```objectivec
@interface MPTutorial : NSObject {
  NSString *tutorialName;
}
```

[Back to Table of Contents ](#table-of-contents)
## Property Attributes

Property attributes should be explicitly listed, and will help new programmers when reading the code.  The order of properties should be atomicity then storage if needed then readonly then getter then setter then nullability.

**Preferred:**

```objectivec
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic, strong, readonly, nullable) NSString *tutorialName;
@property (nonatomic, assign, getter=isReady) BOOL ready;
```

**Not Preferred:**

```objectivec
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`. 
Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

**Preferred:**

```objectivec
@property (nonatomic, copy) NSString *tutorialName;
```

**Not Preferred:**

```objectivec
@property (nonatomic, strong) NSString *tutorialName;
```

[Back to Table of Contents ](#table-of-contents)
## Dot-Notation Syntax

Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Dot-notation should **always** be used for accessing and mutating properties, as it makes code more concise. Bracket notation is preferred in all other instances.

**Preferred:**
```objectivec
NSInteger arrayCount = self.array.count;
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not Preferred:**
```objectivec
NSInteger arrayCount = [self.array count];
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

[Back to Table of Contents ](#table-of-contents)
## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values can not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Preferred:**

```objectivec
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
```

**Not Preferred:**

```objectivec
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```

[Back to Table of Contents ](#table-of-contents)
## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**Preferred:**

```objectivec
static NSString * const MPAboutViewControllerCompanyName = @"iBear LLC";

static CGFloat const MPImageThumbnailHeight = 50.0;
```

**Not Preferred:**

```objectivec
#define CompanyName @"iBear LLC"

#define thumbnailHeight 50.0
```

[Back to Table of Contents ](#table-of-contents)
## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`

**For Example:**

```objectivec
typedef NS_ENUM(NSInteger, MPLeftMenuTopItemType) {
  MPLeftMenuTopItemMain,
  MPLeftMenuTopItemShows,
  MPLeftMenuTopItemSchedule
};
```

You can also make explicit value assignments (showing older k-style constant definition):

```objectivec
typedef NS_ENUM(NSInteger, MPGlobalConstants) {
  MPPinSizeMin = 1,
  MPPinSizeMax = 5,
  MPPinCountMin = 100,
  MPPinCountMax = 500,
};
```

Older k-style constant definitions should be **avoided** unless writing CoreFoundation C code (unlikely).

**Not Preferred:**

```objectivec
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
```

[Back to Table of Contents ](#table-of-contents)
## Case Statements

Braces are not required for case statements, unless other case's contains braces.
When a case contains more than one line, braces should be added.

```objectivec
switch (condition) {
	case 1: {
		// ...
		break;
	}
	case 2: {
		// ...
		// Multi-line example using braces
		break;
	}
	case 3: {
		// ...
		break;
	}
	default: {
		// ...
		break;
	}
}
```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

```objectivec
switch (condition) {
	case 1:
		// ...
		// ** fall-through! **
	case 2:
		// code executed for values 1 and 2
		break;
	default: 
		// ...	
		break;
}
```

When using an enumerated type for a switch, 'default' is not needed.   For example:

```objectivec
MPLeftMenuTopItemType menuType = MPLeftMenuTopItemMain;

switch (menuType) {
  case MPLeftMenuTopItemMain:
    // ...
    break;
  case MPLeftMenuTopItemShows:
    // ...
    break;
  case MPLeftMenuTopItemSchedule:
    // ...
    break;
}
```

[Back to Table of Contents ](#table-of-contents)
## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `MPPrivate` or `private`) should never be used unless extending another class.   The Anonymous category can be shared/exposed for testing using the <headerfile>+Private.h file naming convention.

**For Example:**

```objectivec
@interface MPDetailViewController ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

[Back to Table of Contents ](#table-of-contents)
## Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Preferred:**

```objectivec
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not Preferred:**

```objectivec
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objectivec
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

[Back to Table of Contents ](#table-of-contents)
## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Preferred:**
```objectivec
if (!error) {
  return success;
}
```

**Not Preferred:**
```objectivec
if (!error)
  return success;
```

or

```objectivec
if (!error) return success;
```

### Ternary Operator

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an `if` statement, or refactored into instance variables. In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

Non-boolean variables should be compared against something, and parentheses are added for improved readability.  If the variable being compared is a boolean type, then no parentheses are needed.

**Preferred:**
```objectivec
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
```

**Not Preferred:**
```objectivec
result = a > b ? x = c > d ? c : d : y;
```

[Back to Table of Contents ](#table-of-contents)
## Init Methods

Init methods should follow the convention provided by Apple's generated code template.  A return type of 'instancetype' should also be used instead of 'id'.

```objectivec
- (instancetype)init
{
  self = [super init];
  if (self) {
    // ...
  }
  return self;
}
```

See [Class Constructor Methods](#class-constructor-methods) for link to article on instancetype.

[Back to Table of Contents ](#table-of-contents)
## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

```objectivec
@interface Airplane
+ (instancetype)airplaneWithType:(MPAirplaneType)type;
@end
```

More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

[Back to Table of Contents ](#table-of-contents)
## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Preferred:**

```objectivec
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Not Preferred:**

```objectivec
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
CGRect frame = CGRectMake(CGRectGetMinX(frame), CGRectGetMinY(frame), CGRectGetWidth(frame), CGRectGetHeight(frame));
```

[Back to Table of Contents ](#table-of-contents)
## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Preferred:**

```objectivec
- (void)someMethod {
  if (![someOther boolValue]) {
	return;
  }

  //Do something important
}
```

**Not Preferred:**

```objectivec
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```

[Back to Table of Contents ](#table-of-contents)
## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Preferred:**
```objectivec
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
```

**Not Preferred:**
```objectivec
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

[Back to Table of Contents ](#table-of-contents)
## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objectivec
+ (instancetype)sharedInstance 
{
  static id sharedInstance = nil;
  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

[Back to Table of Contents ](#table-of-contents)
## Line Breaks

Line breaks are an important topic since this style guide is focused for print and online readability.

For example:
```objectivec
self.productsRequest = [[SKProductsRequest alloc] initWithProductIdentifiers:productIdentifiers];
```
A long line of code like this should be carried on to the second line adhering to this style guide's Spacing section (four spaces).
```objectivec
self.productsRequest = [[SKProductsRequest alloc] 
	initWithProductIdentifiers:productIdentifiers];
```

[Back to Table of Contents ](#table-of-contents)
## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [GitHub](https://github.com/github/objective-c-style-guide)
* [Ray Wenderlich](https://github.com/raywenderlich/objective-c-style-guide)
* [Google](https://github.com/google/styleguide/blob/gh-pages/objcguide.md)
* [The New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [Luke Redpath](http://lukeredpath.co.uk/blog/2011/06/28/my-objective-c-style-guide/)
