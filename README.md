# Property Visibility

## Objectives

1. Connect property visibility to real-world privacy.
2. Learn the purpose of class file duality in Objective-C (`.h` & `.m`), and the nature of the private `@interface` section.
3. Identify the three implicit components of using the `@property` syntax (instance variable, setter, getter).
4. Use the `readonly` and `readwrite` property options to protect a property's setter while making its value publicly visible.

## Public vs Private

So far, all of the properties and methods that we've either shown to you in examples or had you write in labs have been *public*. Like playing around in the open of your front yard, this means that everything you've been doing so far has been *visible* to outside classes. Much of the time, this can be appropriate—but not always.

![](https://upload.wikimedia.org/wikipedia/en/1/18/Private_Property_Sign_on_Fence.jpg)  
—*"Private Property" sign on a chain link fence*, David Lottito, 2010. [Wikipedia Commons](https://en.wikipedia.org/wiki/File:Private_Property_Sign_on_Fence.jpg)

Just like closing the curtains in your house because what you do at home simply isn't anyone else's business, sometimes it's just appropriate to keep a property or method *private*—meaning that it's only accessible within the class that it belongs to. The primary usefulness in programming of keeping properties and methods private is to avoid accidental changes to information without going through the proper channels; it is, in a way, a form of assigning *jurisdiction* over information and behaviors. 

**Advanced:** *Privatizing properties and methods has the added effect of improving security within your application because it leaves fewer opportunities to generate exploits—hacks that take advantage of other programs and their data.*

## `@interface`: `.h` vs `.m`

You've likely wondered by this point why it is that Objective-C requires *two* files for every class. What's the purpose behind the `.h` and the `.m` files? It sure feels redundant some of the time.

Well, the answer to that is **visibility**. Everything contained in the `.h` header file is what's publicly visible. The `@interface` section is just that: a declaration of the available "interface" for interacting with that class or an instance of it. The interface is, in effect, the "control panel" for that class. It's the set of commands and input/output that's available to the *public*, meaning outside the class's own inner workings. Those inner workings of how a class actually functions is its *implementation* (how it accomplishes what it claims to in its interface) which it should keep *private*. 

Imagine, as an example, an old analog car radio:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c6/Car_Radio_of_Analog_Era.jpg/1280px-Car_Radio_of_Analog_Era.jpg)
—*Typical car radio of analog era. For Toyota's car, made by Fujitsu Ten.* [Wikipedia Commons](https://commons.wikimedia.org/wiki/File:Car_Radio_of_Analog_Era.jpg)

That front panel with all of the buttons is its *interface*—the public presentation of all the ways that the object is intended to be used. Its *implementation*, however, is kept privately enclosed in a metal box that slides into the dashboard. This is partly for safety (it would be awful to electrocute a passenger!), but mainly for security: the radio manufacturer doesn't want the internal circuitry to get disturbed! That might cause unexpected behavior. 

Providing an interface about public usability via the `.h` header file is how Objective-C classes share information about themselves to other classes. However, *within* that class file's `.m` implementation file, an optional *private interface* can be used for declaring *private properties*. 

The private `@interface` is placed in the `.m` implementation file between the `#import` lines and the `@implementation` section and must be closed with an `@end` keyword before the `@implementation` keyword. Private properties can be added here without making them visible outside of the class. Private *methods*, however, don't need to be declared in the private interface section—the method names in the `@implementation` section make them visible within the current class file.

So, an simple representation of that radio's interface in Objective-C might look something like this:

```objc
//  FISRadio.h

#import <Foundation/Foundation.h>
#import <CoreGraphics/CGBase.h>   // imports CGFloat

@interface FISRadio : NSObject

@property (nonatomic) CGFloat amFrequency;
@property (nonatomic) CGFloat fmFrequency;

@property (nonatomic) CGFloat swBal;
@property (nonatomic) CGFloat tuning;

@property (nonatomic) CGFloat tone;
@property (nonatomic) CGFloat volume;

@property (nonatomic) BOOL stereo;

@property (nonatomic) CGFloat fmPreset1;
@property (nonatomic) CGFloat fmPreset2;
@property (nonatomic) CGFloat amPreset1;
@property (nonatomic) CGFloat amPreset2;
@property (nonatomic) CGFloat amPreset3;

@end
```

## The Anatomy Of A Property

Using the `@property` syntax, while it can feel verbose, is actually  a shorthand for generating the three components of a property: the instance variable, the "setter" method, and the "getter" method.

#### The Instance Variable

The **instance variable** (also "ivar") is the actual value that the property manages. It is both standard convention and implicit syntax that the instance variable is name after the property, but prepends an underscore character (`_`) for disambiguation. So, `_length` is the *instance variable* managed by the `length` property, and `_count` is the *instance variable* managed by the `count` property.

**Note:** *Do* ***not*** *access an instance variable directly unless writing an initializer (common) or overriding a setter or getter (advanced). It best practice to use dot notation which implicitly calls the setter and getter methods for that property.*

#### The Setter Method

The **setter** is a method that is responsible for *writing to* (or "setting") the instance variable. It is both convention and implicit syntax that the setter is named after the property and prefixed with the word "set". The setter for the `length` property would be called `setLength:`, and the setter for the `count` property would be called `setCount:`. However, dot notation implicitly calls the setter method when used on the left side of an assignment operator:

```objc
// good style

self.length = 10;
```
This is equivalent to:

```objc
// bad style

[self setLength:10];
```

#### The Getter Method

The **getter** is a method that is responsible for *reading from* (or "getting") the instance variable. It is both convention and implicit syntax that the getter is named directly after the property whose value it retrieves (though some `BOOL` properties prepend the word "is" to the getter). The getter for the `length` property would be a method called `length`, and the getter for the `count` property would be called `count`. Dot notation implicitly calls the getter method whenever it is submitted as an argument, or is used on the right side of an assignment operator:

```objc
// good style

NSUInteger thisLength = self.length;
[mutableArray addObject:@(self.length) ];
```
These are equivalent to:

```objc
// bad style

NSUInteger thisLength = [self length];
[mutableArray addObject:@([self length]) ];
```

Some of the methods which we have used in previous examples are actually the getter methods for properties:

```objc
// using getter methods (valid)

NSString *welcome = @"Welcome to the Flatiron School!";
NSString *welcomeUC = [welcome uppercaseString];

NSLog(@"%@", welcomeUC);
```
This is equivalent to:

```objc
// using dot notation (preferred)

NSString *welcome = @"Welcome to the Flatiron School!";

NSLog(@"%@", welcome.uppercaseString);
```
These will both print: `WELCOME TO THE FLATIRON SCHOOL!`.

## The `readonly` & `readwrite` Property Options

For information that needs to be publicly visible, but still under the jurisdiction of the owning class to overwrite or change, the `readonly` and `readwrite` property options can be used.

The `readonly` property option does just that: it makes the property unwritable, or "read only". It accomplishes this by creating the property *without* an associated "setter" method.

To create a read-only property, the `readonly` property option must be added to the public declaration of the property. Since there is no setter method generated, in order to write to the property within the class that owns, the property must be *redeclared* in the private `@interface` section as a `readwrite` property that generates a setter with private visibility.

Since the frequency window in the car radio that we're modeling with the `FISRadio` class is readable but only interactive through the "tuning" dial, it makes sense to express the `amFrequency` and `fmFrequency` properties as `readonly`. We can then provide public methods that privately write to the `readonly` properties in a manner that we can control from within the owning class:

```objc
//  FISRadio.h

#import <Foundation/Foundation.h>
#import <CoreGraphics/CGBase.h>   // imports CGFloat

@interface FISRadio : NSObject

@property (nonatomic, readonly) CGFloat amFrequency;
@property (nonatomic, readonly) CGFloat fmFrequency;

{...}

- (void)defaultFrequencies;
- (void)increaseFrequencies;
- (void)decreaseFrequencies;

@end
```

```objc
//  FISRadio.m

#import "FISRadio.h"

@interface FISRadio ()

@property (nonatomic, readwrite) CGFloat amFrequency;
@property (nonatomic, readwrite) CGFloat fmFrequency;

{...}

@end

@implementation

- (void)defaultFrequencies {
    self.amFrequency = 150.0;
    self.fmFrequency = 91.0;
}

- (void)increaseFrequencies {
    if (self.amFrequency + 0.5 <= 160.0) {
        self.amFrequency += 0.5;
        self.fmFrequency += 0.1;
    }
}

- (void)decreaseFrequencies {
    if (self.amFrequency - 0.5 >= 53.0) {
        self.amFrequency -= 0.5;
        self.fmFrequency -= 0.1;
    }
}

@end
```

**Note:** *The* `readwrite` *keyword in the private redeclaration of  the property isn't strictly necessary since it is the implicitly default, however it is considered a best practice to indicate that the property's public visibility is meant to be* `readonly`.

We can then call these methods from outside the `FISRadio` class to "turn the dial". While we can't *write* to the `amFrequency` and `fmFrequency` methods, we can *view* them:

```objc
//  FISAppDelegate.m

#import "FISAppDelegate.h"
#import "FISRadio.h"

@implementation FISAppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    FISRadio *radio = [[FISRadio alloc] init];
    [radio defaultFrequencies];
    NSLog(@"AM: %.1f, FM: %.1f", radio.amFrequency, radio.fmFrequency);
    
    [radio increaseFrequencies];
    NSLog(@"AM: %.1f, FM: %.1f", radio.amFrequency, radio.fmFrequency);
    
    [radio decreaseFrequencies];
    NSLog(@"AM: %.1f, FM: %.1f", radio.amFrequency, radio.fmFrequency);
    
    [radio decreaseFrequencies];
    NSLog(@"AM: %.1f, FM: %.1f", radio.amFrequency, radio.fmFrequency);
          
    return YES;
}

@end
```
This will print:

```
AM: 150.0, FM: 91.0
AM: 150.5, FM: 91.1
AM: 150.0, FM: 91.0
AM: 149.5, FM: 90.9
```
We have successfully privatized the setter for our frequency properties and provided public methods to provide protected access their values.
