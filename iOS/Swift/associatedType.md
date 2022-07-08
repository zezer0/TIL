# associatedType

>An associated type gives a placeholder name to a type that’s used as part of the protocol. The actual type to use for that associated type isn’t specified until the protocol is adopted

-> associated type은  프로토콜의 일부로 사용되는 유형에 placeholder를 지정합니다. 프로토콜이 채택될 때까지 연결된 유형에 사용할 실제 유형을 지정하지 않습니다.

## protocol 정의

```Swift 
protocol Container {
    associatedtype Item
    
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

해당 프로토콜에서 Item이라는 타입을 정의했지만 어떤 타입인지는 아직 정해지지 않았다. 프로토콜을 준수할 때 해당 타입을 정하겠다는 의미이다.

## Not Generic Struct

```Swift 
struct NonGenericIntStack: Container {
    typealias Item = <#type#>
}
```
프로토콜을 채택하여 준수할 때 Item의 타입을 정해줘야 한다. typealias를 통해 Item의 타입을 정해준다.

```Swift 
struct NonGenericIntStack: Container {
    typealias Item = Int
    
    func append(_ item: Int) {
        
    }
    
    var count: Int
    
    subscript(i: Int) -> Int {
        
    }
}
```

## Generic Struct

```Swift 
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```
Generic을 사용하여 Item의 타입을 정해줄 수 있다.
Type Parameter인 Element를 매개변수와 반환값 유형으로 사용하고 있기 때문에 Item의 타입을 Element의 타입으로 추론할 수 있다.
