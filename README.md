# Objective-C Style Guide

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Misc](#misc)
- [Schema changes](#schema-changes)
- [Git Commit messages](#git-commit-messages)
- [Class Interfaces](#class-interfaces)
- [Delegate interfaces](#delegate-interfaces)
- [KVO](#kvo)
- [Class Implementations](#class-implementations)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Misc

* We use spaces, not tabs. A tab = 2 spaces.

* Dot notation
    * Instance methods: use dot notation for properties and property-ish methods, e.g., `self.textView` and `self.assignmentRequired.boolValue` and `person.bestNameOrYou`, but not for `f = self.openFile`
    * Class methods: don't use dot notation, e.g., `[Client class] and [Class sharedInstance]`

* Fix all warnings: Our builds should be free of warnings. We ignore warnings inside CocoaPods.

* Comments should use backticks ` around class names and other code so they show up nice when styled with Markdown

* Spaces between bitwise OR's `UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight`

* CZ Prefix usage
    * Everyday classes are not prefixed with CZ, `BoatTableViewController`
    * Category names are`UITableView(CZBoatingAdditions)`
    * The category's methods are not; just make sure they're specific enough to avoid collision.

* C functions are CamelCased. If it is private to a file, use `static`

* If return type is a pointer, no space after the `*`

    ```objective-c
    NSString *BoolNumberToString(NSNumber *aNumber)
    ```

* Type declarations with protocols show be formated as:

    ```
    - (void)fooMethod:(id<MyProtocol>)param
    ```

* System framework `#import`s go first, then Pods (using `<Podname/Filename.h>` style), then Caremobile headers (sorted alphabetically).

    ```objective-c
    #import <QuartzCore/QuartzCore.h>
    #import <SSKeychain/SSKeychain.h>
    #import "PostsViewController.h"
    #import "GuestbooksViewController.h"
    #import "PostViewController.h"
    ```

* Interface files should only `#import` other interfaces if *needed* (protocol definition, inheritance, etc). If the interface just references a class, predeclare the class with `@class Foo;` instead of `#import "Foo.h"`.

* Asynchronous methods should include a  `completion:` parameter, whose name is `completionBlock`
    * If the block is the only parameter, use `WithCompletionHandler`, like `highlightWithCompletionHandler:(dispatch_block_t)completionBlock`
    * Otherwise, no `With`, like `uploadImage:(UIImage *)image completion:(dispatch_block_t)completionBlock`

* Leave off the file extension in `[UIImage imageNamed:` calls unless there's a reason to specify

## Schema changes

If you change our CoreData schema (i.e., anything in `Caremobile/Caremobile.xcdatamodeld/*`), please isolate your schema change to 1 commit. This commit should have changes to `Caremobile/Caremobile.xcdatamodeld/*` and `Caremobile.xcodeproj` and **nothing** else. This way, if we need to merge schema changes from multiple branches, it's all in 1 commit.

As a corollary, if you make a schema changes in your branch and later need to make more changes, you should squash all schema changes into a single commit before creating a pull request.

## Git Commit messages

* First line should be <= 50 characters and start with a capitol letter
* Subsequent lines can be used if more description is needed
* No trailing punctuation

## Class Interfaces

* Protocols go on a newline and sorted alphabetically, not indented.

```objective-c
@interface ServerPickerViewController()
<TextEditorViewControllerDelegate, UITableViewDelegate>
@end
```
* Order methods as: properties, line break, class methods, line break, initializers, line break, instance methods
* When defining properties add `nonatomic` first with no spaces until after the )

```
@property(nonatomic,strong) BestClassEver *classy;
```

## Delegate interfaces

* Delegate method names should always start with the name of the class

```objective-c
@protocol CommentTextViewDelegate <NSObject>

- (void)commentTextViewDidTapSend:(CommentTextView *)commentTextView;
- (void)commentTextViewDidCancel:(CommentTextView *)commentTextView;
- (void)commentTextView:(CommentTextView *)commentTextView willChangeHeight:(float)height;

@end
```

## KVO

* Avoid KVO unless the alternatives are worse -- KVO obfuscates control flow and it's difficult to debug

* If you're gonna use it, use Apple's framework methods (see the `NSKeyValueObserving` protocol). Do **not** use the methods provided by `BlocksKit` -- they swizzle `dealloc`. :-(

## Class Implementations

* Put `self = [super init*]` on it's own line

```objective-c
self = [super initWithGreatness:aGreatness]

if (self) {
  ...
```
* Common initializers should be named `commonInitFor<ClassName>` (to avoid accidental overrides by subclasses) and be declared in the `#pragma mark - Lifecycle` section, as in:

```objective-c
@implementation FloatingAddButton

#pragma mark - Lifecycle

- (void)commonInitForFloatingAddButton
{
   ...
}

- (id)initWithFrame:(CGRect)frame
{
  self = [super initWithFrame:frame];

  if (self) {
    [self commonInitForFloatingAddButton];
  }

  return self;
}

- (id)initWithCoder:(NSCoder *)aDecoder
{
  self = [super initWithCoder:aDecoder];

  if (self) {
    [self commonInitForFloatingAddButton];
  }

  return self;
}
```

* `init...` methods (including `commonInit...`) should initialize data members directly (e.g., `_foo = @YES;`) instead of calling a setter method. This avoids any side-effects in the setter method. If assigning to a block or `NSString`, make sure you copy (`_blockMember = [aBlock copy];`)

* Method implementations have their curly brace on the next line
* No spaces between return type and method name

```objective-c
- (void)slideAcrossHood
{
  [self.duke slideAcross:_generalLee];
}
```

* 1 blank line between method implementations:

```objective-c
}

- (void)viewWillAppear:(BOOL)animated
```

* Method name order
    * Class methods first
    * `dealloc` method (if needed, should be rare with ARC)
    * initializers
    * sorted groups of `#pragma mark - XYZ` with 1 blank line above and below the `#pragma`.
        * The `#pragma mark` groups should be sorted as:
            * Class methods
            * Lifecycle
            * Getters
            * Methods
            * All other delegates & data sources
    * Define related methods together within `#pragma` groups

* Case statements needing scope should have the curly brace on the same line as the case:

```objective-c
  switch (row) {
    case 0: {
      ...
      break;
    }
  }
```

* `else` and `else if (YES)`'s start with a carriage return

```objective-c
if (YES) {
  Play(kNSAirGuitar);
}
else if (MAYBE) {
  Play(kNSDrama);
}
else {
  Play(kNSSadTrombone);
}
```

* Be explicit in conditions.

```objective-c
if (generalLee == nil)
if (needsPaintJob == NO)
if (theCreek != nil)
if (shouldJumpTheCreek == YES)
```

instead of

```objective-c
if (!generalLee)
if (!needsPaintJob)
if (theCreek)
if (shouldJumpTheCreek)
```

* Use _ for all instance variables, and put them in the implementation with a line break after `@implementation`
* Prefer properties and auto synthesized instance variables wherever possible

```objective-c
@implementation GeneralLeeViewController
{
  FrontDoor *_frontDoor
}

- (id)initWithWorkingDoors:(BOOL)theDoorsWork
{...
```
