# Learning iOS dev

## Optionals

*1 march 2024*

Optionals are annoying when you start writing Swift code. The compiler will often be shouting about "unwrapping optionals". One way of understanding optionals goes like this:

Swift has opinions. It claims that computer programs crash because they expect data that is not there. Programs expect data that is not there because programmers told them to. But in practice, the data may or may not have been fetched or created yet. Therefore Swift thinks that programmers need discipline on how they declare data holders.

In your code, when you declare a variable, Swift expects that you either give it an initial value, or declare it as an optional. An optional is nil by default—until specified otherwise. When you want to use an optional variable in your code later on, the explicit declaration of that variable as an optional means that you need to write special code to handle the optional. That's what *safe unwrapping* is.

```swift
class MyClass {
    // variable with initial value. No issue
    var number = 1
    // variable without an intial value
    // the type must be declared, plus the optional `?` nature
    var aNumber: Int?
    
    // later on
    
    // safe unwrapping
    if let aNumber = aNumber {
        // safely unwrapped `aNumber`
    }
}
```

You *could* declare an uninitialized variable as non optional using a `!` like this:

```swift
class MyClass {
    var someNumber: Int!
}
```

That would mean that you are sure that by the time you'll use `someNumber` in your code, it *will* have a value that is not `nil`. Use carefully.

Throughout your code, you'll notice that the auto-complete will automatically add an `?` after a method or variable while you type. That means that the compiler isn't sure that these values are indeed defined at that point. Maybe the code that is responsible for creating them could return an error, or maybe they rely on an asynchronous I/O call, of which we can not guarantee a definite result at a specific time. So Swift adds these optional, nudging you to handle cases where values are not what they are expected to be.

## init

*28 February 2024*

You'll see the `init` keyword very often in Swift. What is it?

`Init` stands for initialization. It's how you create an instance of a class. Example:

```swift 
class MyClass {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}
```

That boilerplate code allows you to create an instance of `MyClass` by writing `var myInstance = MyClass(name: "Achraf")`. So `init` is essentially the method that you call when you write `MyClass()`, with some parameters inside `()`.

You can have multiple initializers for the same class, each with a different signature! This feature allows you to create instances of a class in different ways, depending on the context or the specific needs of your code. For example:

```swift
class MyClass {

    // Note the optional type of the properties
    var name: String?
    var location: String?
    // This adjustment is necessary because each initializer sets only one of these properties
    // In Swift, all properties of a class must be initialized during instantiation,
    // either with a non-nil value or as nil for optionals

    init(name: String) {
        self.name = name
    }

    init(location: String) {
        self.location = location
    }
}
```

That way, you can create an instance by writing `var myInstance = MyClass(name: "Achraf")`, or `var myInstance = MyClass(location: "Tangier")`. In actual code, this is how you get APIs where you can create an object in different ways. For example, in SpriteKit, you could create a sprite node by calling `mySpriteNode = SKSpriteNode(texture: myTexture)` or `mySpriteNode = SKSpriteNode(imageNamed: myImageName)`. Each of those correspond to different initializers inside the SKSpriteNode class.

## Enum

*27 February 2024*

Suppose you have a finite list of states for an object. You want to define a specific behavior for each state. How would you implement that?

One way is to use `enum`. An enumeration is a type safe way to tell the compiler that this object has these different cases. Then, when you want to write code for each case, you'll switch through the cases, and the compiler will make sure that you've captured all of them, or at least provided a default fallback behavior. Example:

```swift
enum Direction: String {
    case left
    case up
    case right
    case down
}

func move(_ direction: Direction) {
    switch direction {
    case .left:
        // do something
    case .up:
        // do something
    case .right:
        // do something
    case .down:
        // do something
    }
}
```

Whenever you'll write a piece of code that expects data of type `Direction`, the auto-complete will show you all the applicable options from the `Direction` enum. If you start writing `move(.)`, auto-complete will show you `.left, .up, .right, .down` right after you type `.` inside the function's arguments.

Note that in Swift, the `case` statements are aligned with the `switch` keyword, instead of being indented further. I find it counter-intuitive, but that is the convention in Swift.

## Local web server

*26 February 2024*

If you need to start a web server on your Mac, so you can open HTML pages and test some links and code, you don’t need to download or install anything. You can:

- Command + right-click the folder where you want to start a web server
- Paste `python3 -m http.server 9000` in the command line, where `9000` is your desired port.

## Static and class: type properties

*23 February 2024*

Suppose you have a class with a method inside:

```swift
class MyClass {
    func myMethod() {}
}
```

If you write `MyClass.myMethod()` somewhere, calling your method on the class itself, the compiler wouldn’t auto-complete, and the call wouldn't work anyways. Calling that method would only work on *instances* of your class: 

```swift
var myClass = MyClass() // by convention, an instance name start with a lowercase
myClass.myMethod() // this works
```

If you want a method that you can call on the class type itself, you need to prefix that function with `static`:

```swift
class MyClass {
    static func typeMethod() {} // belongs to the type itself
}

MyClass.typeMethod() // now this works
```

This is a case where the technical keyword (`static`) isn't intuitive.

Update *2 march 2024*: it's called `static` because subclasses (not instances!) of the class can not override a method or property prefixed with `static`. You can however define type properties that *can* be overridden by subclasses if you use the keyword `class` instead of `static`. For example:

```swift
class MyClass {
    class var typeVariable: String {
        return "Welcome!"
    }
}

class MySubclass: MyClass {
    override class var typeVariable: String {
        return "Welcome to MySubclass!"
    }
}

print(MyClass.typeVariable) // Prints "Welcome!"
print(MySubclass.typeVariable) // Prints "Welcome to MySubclass!"
```

## Inheritance list

*22 February 2024*

When you write `struct MyStruct: Codable {}` or `class MyScene: SKScene {}`, the location after the `:`, usually where you specify the type or super class of an object, is called "inheritance list". Types, custom types, or protocols are all added to the inheritance list.

## Codable

*21 February 2024*

If you need to store something on disk, share data from your code to somewhere else, or recover your state from a previous session, the `Codable` protocol is key.

`Codable` is a protocol in Swift that enables you to encode and decode your data types (like structs, enums, and classes) into and from external representations such as JSON, XML, plist, or custom binary formats. It's essentially about transforming your data into a format that can be stored or transmitted and then reconstructed later.  The `Codable` protocol is a type alias for the `Encodable` and `Decodable` protocols, allowing both encoding and decoding capabilities to be implemented in a single conformance.

Some types such as `String` or `Int` conform to `Codable` out of the box (intuitive! We can store them as is in JSON or another exchange format). But other custom or framework dependent types such as `Range`, `CGSize`, or `CGAffineTransform`, require additional work to make them encodable and decodable.

Another key concepts linked to storing and restoring data are "serialization" and "deserialization". These processes refer to converting structured data into a format suitable for storage or transmission (serialization) and converting it back into usable data after retrieval or receipt (deserialization).

If your object has to conform to `Codable` but its content isn't Codable out of the box, you need to implement custom logic using two functions:

```swift
struct ObjectDescription: Codable {
    // CGSize is not Codable by default
    var size: CGSize
    
    // When you conform a custom type to Codable, Swift looks for an enum
    // that conforms to CodingKey. You can use these to define custom key names
    // in your external representation. If this enum doesn't exist, Swift uses
    // the property names as they are.
    enum CodingKeys: String, CodingKey {
        // the external representation will key `width` as "storedWidth"
        case width = "storedWidth"
        case height = "storedHeight"
    }
        
    // serialization
    func encode(to encoder: Encoder) throws {
        // we use .self to pass the type of CodingKeys as parameter to keyedBy
        // because keyedBy expect a type as a value
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(size.width, forKey: .width)
        try container.encode(size.height, forKey: .height)
    }
    
    // deserialization
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let storedWidth = try container.decode(CGFloat.self, forKey: .width)
        let storedHeight = try container.decode(CGFloat.self, forKey: .height)
        self.size = CGSize(width: storedWidth, height: storedHeight)
    }
}
```

The `encode(to:)` and `init(from:)` functions are the functions that you need to implement for objects that do not conform to `Codable` out of the box. These functions can also serve other use cases :

- You can use them to do selective encoding and decoding. Any properties not explicitly handled in these methods will not be encoded and decoded.
- You can use them to apply data transformation, which can be useful for partial updates, securing sensitive information, or dealing with version compatibility.

## Sorting a dictionary

*21 February 2024*

You may have data in the form of several `key: value`, i.e. a collection of key/value pairs, aka a dictionary. You may need to do some operations on the key/value pairs, like looping through the keys. Such operations require your data to be presented in a form that the operation understands unequivocally. Typically you'd need to make an array out of a dictionary, given some requirements:

```swift
let myDictionary = ["b": 2, "a": 1, "c": 3]

// produce an array out of a dictionary, by ordering the keys
let sortedArray = myDictionary.sorted { $0.key < $1.key }
print(sortedArray) // [("a", 1), ("b", 2), ("c", 3)]

// you can also sort the value instead of the keys
let sortedValues = myDictionary.sorted { $0.value < $1.value }
print(sortedValues) // [("a", 1), ("b", 2), ("c", 3)]
```

Sorting a dictionary is useful in scenarios where the unordered nature of dictionaries doesn't suit your needs, such as when generating user interface elements that list content in a sorted manner.

## Xcode shortcuts

*Started 17 February 2024*

- Control + I : auto-indent code
- Control + M : format objects into multiple lines. Useful for function signatures or hierarchical data structures
- Control + 6 : search your methods and symbols 
- Command + 0 : show/hide the file navigator
- Command + Option + Enter : show/hide the live preview
- Command + Option + [ : move a line up
- Command + Option + ] : move a line down

## Dealing with optionals and unwrapping

*12 February 2024*

With iOS Swift development, there are many methods that return an optional value. That means that the method may or may not return an object of the expected type. Dealing with optional values is called "unwrapping", and you'll see that word coming up in the compiler very often.

One way to deal with that is "force unwrapping", where we add a `!` at the end of the method chain to tell the compiler "I am sure the return I expect will in fact be returned." But that is not safe, and it is better to "safely unwrap" the optional.

Here are different ways of dealing with optional returns:

```swift
// Optional Binding
if let result = myMethod.result? {
    // do something with result
} else {
    // handle case
}

// Optional Chaining with Default Value
// The default value is provided using `??`, the nil coalescing operator
// The default value must be of the some type as the expected return type
let result = myMethod.result? ?? "Default Value or Message"
// do something with result
```

## Array methods

*4 February 2024*

```swift
// find the first element in an array
let filterName = list.first(where: { $0.name == filter })

// Find the index, i.e. position of an element
let index = existingFilters.firstIndex(where: { $0.filter.name == filter })
```

## Is

*1 February 2024*

We can use the Swift keyword `is` to check the type of an object.

```swift
let someValue: Any = 42

if someValue is Int {
    print("It's an integer!")
} else {
    print("It's not an integer.")
}
```

Now imagine a dictionary (an array of key/value pairs) where some keys have a nested dictionary as their value. If we wanted to list only the keys that have a dictionary as a value, we could write:

```swift
let myDictionary: [String: Any] = // an existing dictionary

let nestedDictionaries = myDictionary.filter { $0.value is [String: Any] } // returns a dictionary with the keys that have themselves a dictionary as a value
```

## Code bloat

*1 February 2024*

I caught myself in an act of code bloat.

I needed a custom API to apply Core Image filters in SpriteKit and SwiftUI. One of the functionalities I wanted is to define default values and ranges for a curated list of filters. That means coming up with a data structure that stores each chosen filter with its associated meta data such as its keys, the type of value they take, the typical range of the values, and a default value.

While exploring Core Image API, I found that Core Image itself has methods such as `filter.attributes`, which returns a dictionary of information about the filter. Here are the attributes of the `CIZoomBlur` filter:

```swift
"inputCenter": {
    CIAttributeClass = CIVector;
    CIAttributeDefault = "[150 150]";
    CIAttributeDescription = "The center of the effect as x and y pixel coordinates.";
    CIAttributeDisplayName = Center;
    CIAttributeType = CIAttributeTypePosition;
},
"CIAttributeFilterAvailable_Mac": 10.4,
"CIAttributeFilterDisplayName": Zoom Blur,
"CIAttributeReferenceDocumentation": http://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CoreImageFilterReference/index.html#//apple_ref/doc/filter/ci/CIZoomBlur,
"CIAttributeFilterName": CIZoomBlur,
"CIAttributeFilterAvailable_iOS": 8.3,
"CIAttributeFilterCategories": <__NSArrayI_Transfer 0x60000029f820>(
    CICategoryBlur,
    CICategoryStillImage,
    CICategoryVideo,
    CICategoryBuiltIn,
    CICategoryHighDynamicRange
),
"inputAmount": {
    CIAttributeClass = NSNumber;
    CIAttributeDefault = 20;
    CIAttributeDescription = "The zoom-in amount. Larger values result in more zooming in.";
    CIAttributeDisplayName = Amount;
    CIAttributeIdentity = 0;
    CIAttributeSliderMax = 200;
    CIAttributeSliderMin = "-200";
    CIAttributeType = CIAttributeTypeDistance;
},
"inputImage": {
    CIAttributeClass = CIImage;
    CIAttributeDescription = "The image to use as an input for the effect.";
    CIAttributeDisplayName = Image;
    CIAttributeType = CIAttributeTypeImage;
}
```

And here is the work in progress data structure I was writing myself:

```swift
struct FilterItem {
    let filter: CIFilter
    let displayName: String
    let parameterRanges: [String: ClosedRange<Double>]? /// an optional dictionary
    let parameterDefaults: [String: Any]? /// an optional dictionary
    
    init(
        filter: CIFilter,
        displayName: String,
        parameterRanges: [String: ClosedRange<Double>]? = nil,
        parameterDefaults: [String: Any]? = nil
    ) {
        self.filter = filter
        self.displayName = displayName
        self.parameterRanges = parameterRanges
        self.parameterDefaults = parameterDefaults
    }
}

let filtersList: [FilterItem] = [
    FilterItem(
        filter: CIFilter(name: "CIZoomBlur")!,
        displayName: "Zoom Blur",
        parameterRanges: ["inputAmount": -50...50],
        parameterDefaults: ["inputCenter": CIVector(x: 150, y: 150), "inputAmount": 20]
    )
]
```

Before finding the Core Image attribute method, I had to search on the web, try many commands and functions in SpriteKit, see how filters behave, learn by trial and error, in order to find the applicable values of a filter. Notice how I was "reinventing" some of the meta data of a filter, such as `displayName`, which would be used in SwiftUI as a label.

I was effectively trying to write my own data structure for a structure that already existed in iOS. But, and this is the interesting part, I had to do it myself in order to understand it and get an intuition for how a filter works. Just using the Core Image filter attribute wouldn't have helped me that much if I didn't go through the trouble of writing my own wrapper around a filter.

It is the very act of understanding and self-appropriating a code structure that I found myself writing an abstraction layer on top of an abstraction layer. That code bloat would probably introduce additional compute time, and possibly errors if my data and the native data drift away from each other. But still, I needed to write my own wrapper that fits my work in progress mental model!

## Key value

*1 February 2024*

Consider a data structure like this:

```swift
let parameterValues: ["inputRadius": 60, "inputAmount": 10, "inputIntensity": 100]
```

That's a dictionary. If you have a command that must apply values of that dictionary to their corresponding keys, you'd loop through the dictionary like this:

```swift
for (key, value) in parameterValues {
    // a command that takes a pair of key/value
    myCommand.setValue(value, forKey: key)
}
```

I used this pattern with Core Image. In Core Image, you can change the value of a filter's key by using a command like `filter.setValue(0.9, forKey: kCIInputIntensityKey)`.

## Array methods

*31 January 2024*

Suppose you have an array like this:

```swift
let myArray = [
    [
        "name" : "achraf",
        "occupation" : "please clarify"
    ],
    [
        "name" : "missing",
        "occupation" : "10x programmer"
    ],
]
```

How do you get the second item of the array? How do you select a specific item in an array? You can write this:

```swift
func findItemInArray() {
    if let arrayItem = myArray.first(where: { $0["name"] == "achraf"}) {
        print(arrayItem)
    } else {
        print("array item not found.")
    }
}
```

You can make the search more safe by using type casting:

```swift
if let arrayItem = myArray.first(where: { $0["name"] as? String == "achraf"}) {
```

Notice how we use `as? String`. This tells the search pattern to check if the value of key `"name"` is of type `String`. 

## SpriteKit scene size

*21 January 2024*

How should I size my SpriteKit scene? How does scene size affect performance? Will my nodes' position be constrained by the scene size? If a physics body falls under gravity, will it continue to fall indefinitely?

If we command-click on `SKScene` in a SpriteKit code, we can bring up the header information for the class:

> A scene is infinitely large, but it has a viewport that is the frame through which you present the content of the scene. The passed in size defines the size of this viewport that you use to present the scene.
>

An older version of the header, [quoted here](https://stackoverflow.com/a/33447352/420176), reads:

> To display different portions of your scene, move the contents relative to the viewport. One way to do that is to create a SKNode to function as a viewport transformation. That node should have all visible contents parented under it.

That version probably predates the introduction of `SKCameraNode`, since a SpriteKit camera does essentially that. Now regarding the relation between the scene size and the viewport size, we can read this in the same header file:

> fill: Scale the SKScene to fill the entire SKView
>
> aspectFill: Scale the SKScene to fill the SKView while preserving the scene's aspect ratio. Some cropping may occur if the view has a different aspect ratio.
>
> aspectFit: Scale the SKScene to fit within the SKView while preserving the scene's aspect ratio. Some letterboxing may occur if the view has a different aspect ratio.
>
> resizeFill: Modify the SKScene's actual size to exactly match the SKView.

So a SpriteKit scene is an infinite canvas by default. The part of the scene that is being drawn and rendered is the view (`SKView`). A scene can either be scaled to fit a view (one of the 4 `scaleMode`), or be drawn through a camera frame that determines which crop of the scene is in view.

Regardless of scene size, objects can be positioned freely without limit. Does positioning objects tens or hundreds of thousands of points away from each other have an impact on memory consumption and performance? I don't know yet.

## private, fileprivate, internal, public

*20 January 2024*

Swift has keywords that tell the access level of a class, method, property, or other entity.

Access control is a way to cognitively organize your code. It invites you to clarify–and lets you know when reading your code later on–the intended scope of visibility of your entities. This makes it clearer how parts of your code may or may not communicate across files and modules. Here's a list of interesting access level modifiers:

```swift
// can be read (get) from the outside the scope,
// but can be written (set) only from within the scope
private(set) var myVariable

// entities outside the defining module of this class can subclass and override
// members of this class. The most permissive access level in Swift
open class MyClass {}
```

See also this interesting link with static and dynamic linking in Swift:

> Swift’s `fileprivate`, `internal`, and `public` correspond, ish, to access levels supported by [libraries] linkers: “only available in this file”, “only available in this library”, and “available to clients”.

Source: https://belkadan.com/blog/2022/02/Dynamic-Linking-and-Static-Linking/

## Stride

*15 January 2024*

This is an interesting loop constructor in Swift: `for in stride()`. We can use the stride loop to run over a range of numbers using an increment. For example:

```swift
for i in stride(from: 0, to: 10, by: 2) { // excludes upper bound 10 from the loop
    print(i)
}

for i in stride(from: 0, through: 10, by: 2) { // includes the upper bound 0.5 in the loop
    print(i)
}
```

## Core Image filters

*15 January 2024*

- [A list of CI Filters](https://gist.github.com/Umity/c42920a236ad4fdd950492678a9136fa), GitHub
- [Core Image Filter Reference](https://developer.apple.com/library/archive/documentation/GraphicsImaging/Reference/CoreImageFilterReference/index.html#//apple_ref/doc/uid/TP30000136-SW29), Apple

## Expand a type

*18 December 2023*

"Expanding a type" is a programmer's way to say: adding methods, properties, or other mechanisms to an object that you have created. For example, when you create a class and name it "myClass", "myClass" becomes a type that you have defined.

Therefore expanding a type means elaborating on custom created objects. Swift has some feature that automates expansions of types, in order to make them do more that what the author wrote them to do explicitly. Macros are an example of such a feature. The following code "expands" on the class `FoodTruckModel` through the use of the `@Observable` macro:

```swift
@Observable class FoodTruckModel {    
    var orders: [Order] = []
    var donuts = Donut.all
}
```

## Accidental shader

*16 December 2023*

I run this code from: https://www.hackingwithswift.com/books/ios-swiftui/animating-gestures

```swift
struct ContentView: View {
    let letters = Array("Hello SwiftUI")
    @State private var enabled = false
    @State private var dragAmount = CGSize.zero

    var body: some View {
        HStack(spacing: 0) {
            ForEach(0..<letters.count, id: \.self) { num in
                Text(String(letters[num]))
                    .padding(5)
                    .font(.title)
                    .background(enabled ? .blue : .red)
                    .offset(dragAmount)
                    .animation(.linear.delay(Double(num) / 20), value: dragAmount)
            }
        }
        .gesture(
            DragGesture()
                .onChanged { dragAmount = $0.translation }
                .onEnded { _ in
                    dragAmount = .zero
                    enabled.toggle()
                }
        )
    }
}
```

At some point while playing with the live preview,  I started making swift and short up and down motions with the mouse (see "Accidental shader" video screen recording). The resulting behavior looked like wind blowing on a flag. The combination of the SwiftUI setup and a particular user input pattern generated a behavior that looked like a recognizable natural phenomenon.

## Example in struggling

*13 December 2023*

I want to implement this multi-touch UI control. For that, I need to build a sufficient mental map of the available SwiftUI building blocks, how they assemble together, and what are the recommended practices.

![Selector component](https://www.achrafkassioui.com/images/Selector%20behavior.png)

## SpriteKit text blurriness

*5 December 2023*

SpriteKit nodes of type `SKLabelNode`, aka text, get blurry when the camera is zoomed in. See https://stackoverflow.com/a/72286447/420176

One hack around it is to internally multiply the font size by a scale factor, then scale the node down by the same factor, to get a better rendering when zoomed in.

```Swift
let myText = SKLabelNode()
let textScaleFactor: CGFloat = 5.0
myText.fontSize = 28 * textScaleFactor
myText.name = "myText"
myText.text = "Hello"
myText.fontName = "Impact"
myText.xScale = 1 / textScaleFactor
myText.yScale = 1 / textScaleFactor
addChild(myText)
```

Further testing is required to see how the scaling may affect other behaviors such as physics simulations.

Update: while adding many emojis to the same `SKLabelNode`, the app crashed with an error about Metal's maximum texture size. I believe that given the internal multi-sampling introduced above, and emojis being images, having many of them in the same SpriteKit node eventually exceeds the rendering engine constraints. `SKLabelNode` text length must be limited.

https://en.wikipedia.org/wiki/Spatial_anti-aliasing#Super_sampling_/_full-scene_anti-aliasing

## 9223372036854775807

*27 November 2023*

That's the biggest number that can be stored in an integer variable in Swift.

```Swift
var number = Int.max
print(number) // outputs 9223372036854775807
```

## String interpolation

*19 November 2023*

String interpolation is the way you inject a value of some type into a placeholder that expects a string type. It's the fancy name for this common operation:

```Swift
var myVariable: Int = 10
Text("\(myVariable)") // in SwiftUI, displays "10"
```

Note that string interpolation requires to write the value inside a `""`, which is called a string literal.

## inout

*18 November 2023*

This is not a typo. `inout` not `input`. Consider the following two blocks of code:

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

`inout` tells Swift to modify the value of the passed parameter itself. `&` is the way to make sure the user is aware that the function they are calling uses `inout`, therefore changing the value of the original variable.

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

This feature is used extensively in Apple frameworks. For example here in UIKit:

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

Swift has a specific syntactical construction for functions that call a block of code (a closure) as an argument.

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
- The closure is the piece of code that was passed as an argument in the function signature

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

Protocols are one way to structure your code. You can create a protocol, define a method inside that protocol, and specify that a class or object conform to that protocol. Then, Swift compiler will alert you if the class does not conform to the protocol, i.e. that you haven't implemented all the methods that the class needs in order to conform to the requirement.

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

When they are user defined, protocols are cognitive and organizational tools. They help you manage your code. Other protocols come built-in with Swift, such as `Comparable`.

An object conforms to `Comparable` if its content can be compared with relational operators such as `>` or `>=`. `Comparable` itself builds upon another protocol, `Equatable`, which requires the implementation of the `==` operator, i.e. a way to define the concept of equality between objects.

## Override

*7 August 2023*

Throughout iOS and Apple frameworks, you'll see code like:

```swift 
class MyScene: SKScene {
    override func didMove(to view: SKView) { /* code */ }
}
```

Notice the `override`. A framework would come with predefined classes, such as `SKScene` here, a SpriteKit class, which you can "subclass" to create your own. Inside your subclass, you get access to predefined methods inherited from the parent class, such as `didMove`. And typically, you "override" them in order to write your own custom setup. 

Here is another override to handle touch events inside a SpriteKit scene:

```swift
class MyScene: SKScene {
	override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) { /* code */ }
}
```

A SpriteKit `SKScene` is of type `SKNode`, itself of type `UIResponder`. According to Apple documentation: 

> Responder objects — instances of UIResponder — constitute the event-handling backbone of a UIKit app. [...] As events occur, UIKit dispatches them to your app’s responder objects for handling.
>
> There are several kinds of events, including touch events, motion events, remote-control events, and press events. To handle a specific type of event, a responder must override the corresponding methods. For example, to handle touch events, a responder implements the touchesBegan(_:with:)
>
> https://developer.apple.com/documentation/uikit/uiresponder

Class inheritances and polymorphism!

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

- 🎬 [Miguel de Icaza talks about Swift in general](https://media.ccc.de/v/godotcon2023-57866-swift-godot-fixing-the-multi-million-dollar-mistake#t=3280), and advocates for using Swift for The Godot game engine. 2023.
- 💻 You can use [this online Swift compiler](https://www.programiz.com/swift/online-compiler/) to run some code. 
- 🎬 📒 [Good basic tutorial](https://www.youtube.com/watch?v=Sb7ytck3gMA) on how to present a SpriteKit scene in a SwiftUI view, by Daniel Budd. 2023.



