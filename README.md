# Learning iOS dev

This is a collection of notes I take while learning native iOS development, starting from June 2023. They are purposefully written in a candid style, to honestly capture the mental model and fuzziness of learning.

## 9223372036854775807

*27 November 2023*

That's the biggest number that can be stored in an integer variable in Swift.

```Swift
var number = Int.max
print(number) // outputs 9223372036854775807
```

## String interpolation

*19 November 2023*

String interpolation is the way you inject a value of some type into a placeholder that expects a string type. It's fancy name that refers to a common operation:

```Swift
var myVariable: Int = 10
Text("\(myVariable)") // in SwiftUI, displays "10"
```

Note that string interpolation requires to write the value inside a `""`, i.e. a string literal.

## inout

*18 November 2023*

This is not a typo. It's indeed `inout`, not `input`. Consider the following two blocks of code:

```Swift
var A: Int = 3
var B: Int = 4

func add(term1: Int, term2: Int) {
    A = term1 + term2
}

add(term1: A, term2: B)
print(A) // Output: 7
```
```Swift
var A: Int = 3
var B: Int = 4

// Notice the use of `inout`
func addInPlace(term1: inout Int, term2: Int) {
    term1 = term1 + term2
}

// Notice the use of `&`
addInPlace(term1: &A, term2: B)
print(A) // Output: 7
```

The two blocks give the same output. The difference is the usage of `inout` and `&` in the second block.

`inout` tells Swift to modify the value of the passed parameter itself. And `&` is a way to make sure the user is aware that the function they are calling uses `inout`, therefore changing the value of the original variable.

`inout` allows the modification of a value, without copying it, and without using a reference or a global variable inside the function's closure.

## Different names for the same parameter

*8 November 2023*

In Swift, we can define a function using 2 different names for the same parameter:

- One name used externally when calling a function
- One name used internally in the definition of the function

For example:

```swift
func myFunction(A B: Int) -> Int {
    var myValue = B + 2
    return myValue
}
// A is the external name of B
// B is the internal name used within the function definition
// A and B are both the same parameter

myFunction(A: 5)
// I call the function using the external name
// Returns 7
```

This feature is used extensively in Apple frameworks. For example here with UIKit:

```swift
override func didMove(to view: SKView) {
    // do something with the view parameter
}
```

The `to` is the external parameter's name, while `view` is the internal one.

## The underscore _

*8 November 2023*

Many times in Swift code, I find this ugly symbol in function signatures:

```swift
func greet(_ name: String) {
    print("Hello, \(name)")
}
```

What is the underscore for? Its purpose is to pass a value to a function without specifically naming the parameter. 

Technically, if a function has a signature in the form of `func greet(name: String)`, then calling the function necessarily requires to specify the name of the parameter, like `greet(name: "Achraf")`. But if you add `_` before the name of the parameter, then we can call the function without naming that parameter, as in `greet("Achraf")`.

The underscore is also used to name internal variables in a function. It's a Swift common pattern, not a mandatory rule. It's a way of telling yourself and other people that the variable is for internal use only, and does not belong in another context.

Example:

```swift
func greet(_ name: String) {
    var _name = name.lowercased()
    print("Hello, \(_name)")
}

greet("ACHRAF")
```

## People I'd like to chat with

*3 November 2023*

I'd like to ask these app makers and some programmers in general about their experiences and knowledge.

People I've talked to about iOS programming:
- John Knowles *(started end of October 2023)*

People I'd like to talk to:
- UIBuzz podcaster, about SpriteKit and game development on iOS (*7 November 2023*)
- Makers of Medly, the music app (*3 November 2023*)

## Computed properties

*2 November 2023*

A computed property is a variable whose value isn't fixed, but instead computed whenever it is called. We use braces `{}` to define it instead of equal `= `.

```swift
var center: CGPoint {
    CGPoint(x: size.width / 2.0, y: size.height / 2.0)
}
// the computed property is "center"
// its type is "CGPoint"
// its value is dynamically generated whenever it's called
```

A computer property is also called an accessor. Source: https://developer.apple.com/tutorials/developinswifttutorials/custom-views

## Swift trailing closure

*4 September 2023*

Swift has a specific syntactical construction for functions that call a block of code (a closure).

``` swift
func travel(action: () -> Void) {
    print("step 1")
    action()
    print("step 2")
}

travel {
    print("in betwen step")
}
```

Notice:

- The definition of `travel` tells the order of execution of code
- The call of `travel` is a shorthand syntax made without `()` and immediately trailed by a closure `{}`
- The closure is the piece of code that was passed as parameter in the function signature

In Swift, when the last (or only) argument to a function is a closure, we can use the shorthand syntax. The shorthand syntax is used in SwiftUI such as:

```swift
VStack {

}
// is equivalent to
VStack() {
    
}

// sometimes the function is called with parameters in the initialiser such as
VStack(.leading) {
    
}
```

## App scaffolding in SwiftUI

*3 September 2023*

```swift
import SwiftUI

@main
struct MyAppName: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

struct ContentView: View {
    var body: some View {
        Text("Hello")
            .padding()
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

## Function overloading

*23 August 2023*

In Swift, since it is a strongly typed language, the expected type of a function parameter effectively discriminates that function. We can have functions with the same name, but different expected type as argument. This allows to execute a different code with the same function signature depending on the type of the passed on parameter.

Example:

```swift
extension MyClass {
    // For String
    func displayMessage(_ message: String) {
        displayLogMessage(message)
    }	
    // For CGPoint
    func displayMessage(_ point: CGPoint) {
        let formattedMessage = "CGPoint(x: \(point.x), y: \(point.y))"
        displayLogMessage(formattedMessage)
    }	
    // For CGFloat
    func displayMessage(_ value: CGFloat) {
        let formattedMessage = "CGFloat: \(value)"
        displayLogMessage(formattedMessage)
    }

    private func displayLogMessage(_ message: String) {
        // generic code
    }
}

// Usage:
displayMessage(/* typed information */)
```

## Metal

*17 August 2023*

I got this runtime error while trying to output a 18000*18000 image to disk using SpriteKit:

```
-[MTLTextureDescriptorInternal validateWithDevice:]:1344: failed assertion `Texture Descriptor Validation
MTLTextureDescriptor has width (18000) greater than the maximum allowed size of 8192.
MTLTextureDescriptor has height (18000) greater than the maximum allowed size of 8192.
'
```

I have reached Metal texture size limitations! Woohoo!

## Static

*15 August 2023*

Declaring a function as static inside a class is another way of organizing code. It's another cognitive and complexity management tool. A static method does not hold specific values for each instance of the class. Instead, it is a general method of the class itself. So static may be used to make sure that the method does not access or expect particular values from different instances.

## Notification pattern

*14 August 2023*

```swift
static let myNotification = "myNotification"

// run this to broadcast a notification
NotificationCenter.default.post(
    Notification(
        name: NSNotification.Name(myNotification),
        object: nil)
)

// add an oberver for the notification elsewhere
NotificationCenter.default.addObserver(
    self,
    selector: #selector(callToExecute),
    name: Notification.Name(myNotification),
    object: nil
)

// run this when the observer receives notification
@objc func callToExecute() {
    
}
```

## Declaring variables

*13 August 2023*

Variables and properties can be declared. Each variable should be initialized to a value, or declared without a value provided a type is declared. Moreover, after the type, a "!" or "?" are required.

```swift
var myVariable1 = 0
var myVariable2: Type! // This variable is expected to have a value by the time you use it, but could be nil initially.
var myVariable3: Type? // This variable might or might not ever get a value.
```

## Protocols

*11 August 2023*

Protocols are one way to structure your code. You can create a protocol, define a method inside that protocol, and specify that a class or object conform to that protocol. Then, Swift compiler will alert you if the class does not conform to the protocol, i.e. that you haven't implemented all the methods that the class needs in order to conform to the protocol.

So my understanding is that protocols are not features of the machinery, but rather, protocols are cognitive and organizational tools. They help yo manage your code.

A protocol is a list of rules or requirements that an object has to follow.

```swift
// define a protocol
protocol MyProtocol {
	func interact()
}

// conform to a protocol
class myClass: MyProtocol {
	func interact() {
	}	
}
```

## Override inheritance

*7 August 2023*

The reason touch handling inside a SpriteKit scene instance is done with the following code:

```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
	// code
}
```

A scene is a SpriteKit node, nodes are of type SKNode, which inherit from UIResponder. According to Apple documentation: 

> Responder objects — instances of UIResponder — constitute the event-handling backbone of a UIKit app. [...] As events occur, UIKit dispatches them to your app’s responder objects for handling.
>
> There are several kinds of events, including touch events, motion events, remote-control events, and press events. To handle a specific type of event, a responder must override the corresponding methods. For example, to handle touch events, a responder implements the touchesBegan(_:with:)
>
> https://developer.apple.com/documentation/uikit/uiresponder

That's why we override!

## Bitwise operations

*7 August 2023*

It's interesting to see low-level computing optimization scoop up in a high-level programming API. For example, in SpriteKit, we can define different categories inside a physics simulation, in order to tell which body can collide with which.

By convention, these categories are given names that are bitwise operations friendly. Such as

```swift
struct PhysicsCategory {
	static let None:  UInt32 = 0 // 00000000
	static let Cat:   UInt32 = 0b1 // 00000001
	static let Block: UInt32 = 0b10 // 00000010
	static let Bed:   UInt32 = 0b100 // 00000100
}
```

Notice the binary equivalent for each category name. These expressions allow for efficient, low power bitwise operation: https://en.wikipedia.org/wiki/Bitwise_operation. It also provides handy operations in code:

> This is very handy and allows you to easily combine categories. For example, when you want to specify that the cat should collide with all block bodies and the bed, you can say the collision bit mask for the cat is PhysicsCategory.Block | PhysicsCategory.Bed (read this as “block OR bed”), which produces the logical OR of the two values
>
> 2D Apple Games by Tutorials (2017), p. 228

## Less verbose Xcode console

*5 August 2023*

To disable the ton of OS level messages Xcode displays on the console:

Product > Scheme > Edit Scheme... > Run > Arguments 

In the "Environment Variables", add this item:

- Name: OS_ACTIVITY_MODE
- Value: disable

## Value type VS reference type

*5 August 2023*

Can be useful to understand and use for The Tool.

https://developer.apple.com/swift/blog/?id=10

## Swift methods and commands

*Started 18 July 2023*

```swift
// π, aka 3.14....
.pi
```

## UIKit methods

*Started 18 July 2023*

```swift
// Haptic feedback
let generator = UIImpactFeedbackGenerator(style: .light)
generator.impactOccurred()
```

## Present a view

*13 July 2023*

Confusing programming word of the day:

*Present* a view

On iOS using UIKit, we say "present a view" to mean that the view has appeared on screen.

## Touches vs. gestures

*9 July 2023*

In "2D Apple games by Tutorials", page 68, we can implement an interaction method with both `touchesBegan` / `touchesMoved`, and `UITapGestureRecognizer`. They both work, but the behavior is noticeably different.

With single touches, we can tap the screen repeatedly and quickly, and the method will be triggered. Whereas with a gesture recognizer, it seems that quickly and repeatedly tapping the screen doesn't always trigger the method. This is likely because the gesture recognizer is working to discriminate between double taps and other composed touches, and so repeatedly tapping the screen may be interpreted as a double tap, therefore not triggering the method, since that method except a single tap.

## Clamping a position

*9 July 2023*

While learning SpriteKit, I was writing a method that checks if an element has reached or crossed over the edges of the screen:

```swift
func checkElementBoundaries() {}
    let bottomLeft = CGPoint.zero
    let topRight = CGPoint(x: size.width, y: size.height)

    if element.position.x <= bottomLeft.x {
        element.position.x = bottomLeft.x
        velocity.x = -velocity.x
    }		
}
```

Notice the line `element.position.x = bottomLeft.x`. Why do we set the element x position to the edge of the screen? We do so as a safety/stability measure. Maybe the element has crossed the edge of the screen at that time. So we move it back to a safe position. This is called **clamping**.

Links:

- Learned from "2D Apple games by Tutorials", page 69
- Full code sample in project "Clamping - Sample code"
- In other areas, "clamping" means "securing in place."

## UIKit view controller

*7 July 2023*

In UIKit, in order to create a view (for more about "views", search for "MVC Model"), we create a function of type "UIViewController". That function is a subclass of the superclass "UIViewController".

For example:

```swift
import UIKit

class myViewController: UIViewController {
  // Code for my view	
}
```

In that subclass, it is customary to:

- Override the method `viewDidLoad()` of the superclass, and put the view setup code there
- But still call `viewDidLoad()` from inside that override, in case `UIViewController` is doing something important there, that does require the view to load indeed.

So we would have this code:

```swift
import UIKit

class myViewController: UIViewController {
	override func viewDidLoad() {
		super.viewDidLoad()
	}	
}
// The "super" means we call a method from the superclass of this subclass
```

### Sources

- https://stackoverflow.com/questions/40151723/why-when-do-we-have-to-call-super-viewdidload
- Book: 2D Apple Games by Tutorials, Page 37

## String insertion and concatenation

*6 July 2023*

In Swift, in order to insert a string of text + a programmatic element, you do this:

```swift
Text("Text")
Text("\(myProgrammaticElement) example")
Text("\(Image(systemName: "clock")) clock")
```

## Disclosure indicators

*5 July 2023*

You know the chevrons next to a navigable button or link? They are called "Disclosure indicators".

## Previews

*29 June 2023*

A preview on the live canvas can be added to any file.

The preview code is of the shape of:

```swift
// PreviewProvider is the protocol this struct must conforms to
struct TheCodeToPreview_Previews: PreviewProvider {
	// a declaration for of the view
	static var previews: some View {
      // a reference to the function to preview
      // or an inline sample code to preview like below 
      Button("Sleep", action: { })
          .buttonStyle(TheCodeToPreview())
          .previewLayout(.sizeThatFits)
          .padding()
  }
}
```


## Custom button and label styles

*29 June 2023*

In order to style buttons and labels, we use `ButtonStyle` and `LabelStyle` and pick from one of the OS choices.

If we want to customize buttons and labels accross a project, we can create a `struct` inside a dedicated file conventionally named `{style-name}ButtonStyle.swift`, and then we use the `makeBody` method inside that `struct`

*Addendum*: one benefit of creating a dedicated file is that we can add a dedicated Preview code at the bottom of the file, so we can check our modification on the live canvas.

Links:

- https://betterprogramming.pub/create-custom-button-styles-in-swiftui-f3778f695266
- https://www.hackingwithswift.com/quick-start/swiftui/customizing-button-with-buttonstyle

## Day 1

*7 may 2022*

How to rename a function? You right-click on the name of the function and you choose "Refactor > Rename"!

One of the first thing I intuitively tried in front of a ContentView.swift file was to rename all "ContentView" references to something else, like "ContentViewer", including the file in the finder. That didn't go well. I lost some time recreating the file and figuring how to place it again in the project.

So how do I rename a function or a block of code that's referenced elsewhere? Apparently, I "right-click>refactor>rename" it!

Thoughts:

- Is this syntax tree editing? Does this mean that Xcode operates at a higher level of abstraction than pure text characters on a text editor?

Links:

- Learnt in https://developer.apple.com/tutorials/app-dev-training/using-stacks-to-arrange-views

## Links and resources

- 🎬 Miguel de Icaza talks about Swift in general, then advocates Swift for The Godot engine: https://media.ccc.de/v/godotcon2023-57866-swift-godot-fixing-the-multi-million-dollar-mistake#t=3280
- 💻 You can use this online Swift compiler to try out some code: https://www.programiz.com/swift/online-compiler/
- 🎬 📒 Good basic tutorial on how to present a SpriteKit scene in a SwiftUI view: https://www.youtube.com/watch?v=Sb7ytck3gMA

