## Protocol Oriented Programming / Writing Custom Delegation

A protocol defines a blueprint of methods, properties, and other requirements that suit a particular task or piece of 
functionality. The protocol can then be adopted by a class, structure, or enumeration to provide an actual implementation of
those requirements. Any type that satisfies the requirements of a protocol is said to conform to that protocol.

In addition to specifying requirements that conforming types must implement, you can extend a protocol to implement some of 
these requirements or to implement additional functionality that conforming types can take advantage of.

## Property Requirements 

A protocol can require any conforming type to provide an instance property or type property with a particular name and type. 
The protocol doesn’t specify whether the property should be a stored property or a computed property—it only specifies the 
required property name and type. The protocol also specifies whether each property must be gettable or gettable and settable.

If a protocol requires a property to be gettable and settable, that property requirement can’t be fulfilled by a constant 
stored property or a read-only computed property. If the protocol only requires a property to be gettable, the requirement 
can be satisfied by any kind of property, and it’s valid for the property to be also settable if this is useful for your own
code.

Property requirements are always declared as variable properties, prefixed with the var keyword. Gettable and settable
properties are indicated by writing { get set } after their type declaration, and gettable properties are indicated by writing
{ get }.


## Objectives 
* understand how a protocol helps us to write more usable code  
* write and make use of protocols in what Apple calls - Protocol Oriented Programming  
* use protocols to write custom delegations in our iOS apps

Here’s an example of a protocol with a single instance property requirement:
```swift 
protocol FullyNamed {
    var fullName: String { get }
}
```

Here’s an example of a simple structure that adopts and conforms to the FullyNamed protocol:
```swift 
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
// john.fullName is "John Appleseed"
```

This example defines a structure called Person, which represents a specific named person. It states that it adopts the 
FullyNamed protocol as part of the first line of its definition.

Each instance of Person has a single stored property called fullName, which is of type String. This matches the single 
requirement of the FullyNamed protocol, and means that Person has correctly conformed to the protocol. (Swift reports an 
error at compile-time if a protocol requirement is not fulfilled.)

Here’s a more complex class, which also adopts and conforms to the FullyNamed protocol:

```swift 
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName is "USS Enterprise"
```

This class implements the fullName property requirement as a computed read-only property for a starship. Each Starship class 
instance stores a mandatory name and an optional prefix. The fullName property uses the prefix value if it exists, and
prepends it to the beginning of name to create a full name for the starship.

## Method Requirements 

Protocols can require specific instance methods and type methods to be implemented by conforming types. These methods are 
written as part of the protocol’s definition in exactly the same way as for normal instance and type methods, but without
curly braces or a method body. Variadic parameters are allowed, subject to the same rules as for normal methods. Default 
values, however, can’t be specified for method parameters within a protocol’s definition.

As with type property requirements, you always prefix type method requirements with the static keyword when they’re defined 
in a protocol. This is true even though type method requirements are prefixed with the class or static keyword when 
implemented by a class:

```swift
protocol Game {
    var playerCount: Int { get }
    func start()
    func end()
    init(numberOfPlayers: Int)
}
```

```swift 
struct Basketball: Game {
    private var numberOfPlayers: Int
    var playerCount: Int {
        return numberOfPlayers
    }
    func start() {
        print("start of the first quarter")
    }
    func end() {
        print("end of the 4th quarter")
    }
    init(numberOfPlayers: Int) {
        self.numberOfPlayers = numberOfPlayers
    }
}

let game = Basketball.init(numberOfPlayers: 10)
game.playerCount
game.start()
game.end()
```

## Initializer Requirements

Protocols can require specific initializers to be implemented by conforming types. You write these initializers as part of 
the protocol’s definition in exactly the same way as for normal initializers, but without curly braces or an initializer 
body

## Delegation 

Delegation is a design pattern that enables a class or structure to hand off (or delegate) some of its responsibilities to an
instance of another type. This design pattern is implemented by defining a protocol that encapsulates the delegated
responsibilities, such that a conforming type (known as a delegate) is guaranteed to provide the functionality that has been 
delegated. Delegation can be used to respond to a particular action, or to retrieve data from an external source without
needing to know the underlying type of that source.

Class that defines custom Delegate 
```swift
protocol SettingsDelegate: AnyObject {
    func darkModeOn()
    func darkModeOff()
    func setLabel(message: String)
}

```
delegate variable conforming class needs to set, similar to tableView.delegate = self 
```swift 
weak var delegate: SettingsDelegate?
```
Trigger action when user selectes a new animal profile image 
```swift 
delegate?.darkModeOn()
```

Conforming Class 
delegate variable needs to be set
```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    guard let destination = segue.destination as? SettingsViewController else { return }
        destination.delegate = self
}
```
As part of comforming to the delegate, its methods need to be implemented 
```swift
extension ViewController: SettingsDelegate {
    func setLabel(message: String) {
        myLabel.text = message
    }
    
    func darkModeOff() {
        myLabel.textColor = .black
        self.view.backgroundColor = .white
        darkModeIsOn = false
    }
    
    func darkModeOn() {
        myLabel.textColor = .white
        self.view.backgroundColor = .black
        darkModeIsOn = true
    }
}
```
]


## Resources 
[Protocols](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267)  

