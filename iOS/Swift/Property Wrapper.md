# Property Wrapper

## Property Wrapper란?
Property Wrapper는 속성이 저장되는 방식을 관리하는 코드와 속성을 정의하는 코드 사이에 분리 계층을 추가합니다. Property Wrapper를 사용하는 경우 래퍼를 정의할 때 관리 코드를 한 번 작성한 다음 여러 속성에 적용하여 해당 관리 코드를 재사용합니다.

Property Wrapper를 정의 하려면 wrappedValue 프로퍼티가 정의되어 있는 struct, enum, class를 만듭니다. 아래 코드에서 TwelveOrLess구조는 래핑하는 값이 항상 12보다 작거나 같은 숫자를 포함하도록 합니다. 더 큰 숫자를 저장하도록 요청하면 대신 12를 저장합니다.

```Swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```
setter는 새 값이 12보다 작거나 같은지 확인하고 getter는 저장된 값을 반환합니다.

> NOTE위 예제에서의 number는 private 변수로 선언되어 있습니다. 따라서 number는 TwelveOrLess의 내부에서만 사용된다는 것이 보장됩니다. TwelveOrLess 외부에서는 number를 바로 사용할 수 없고, wrappedValue의 getter/setter를 사용해 이 값에 접근할 수 있습니다. private에 대한 더 많은 정보는 Access Control에서 보실 수 있습니다.


속성 앞에 Property Wrapper의 이름을 작성하여 적용합니다. 다음은 TweetyOrLess Property Wrapper를 사용하여 크기가 항상 12 이하인지 확인하는 직사각형을 저장하는 구조입니다.

```Swift
struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height)
// Prints "0"

rectangle.height = 10
print(rectangle.height)
// Prints "10"

rectangle.height = 24
print(rectangle.height)
// Prints "12"
```

속성에 Wrapper를 적용하면 컴파일러는 Wrapper를 저장하는 코드와 Wrapper를 통해 속성에 접근할 수 있는 코드를 합성합니다.(Property Wrapper는 래핑된 값을 저장하는 역활을 하므로 그에 대한 합성코드가 없습니다.)
Property Wrapper이름을 앞에 붙이는 방법 이 외에도 Property Wrapper을 사용할 수 있습니다. 예를 들어, 여기 @TwelveOrLess를 property앞에 작성하는 대신 TweeteOrLess 구조로 속성을 명시적으로 감싸줍니다.

```Swift
struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()
    var height: Int {
        get { return _height.wrappedValue }
        set { _height.wrappedValue = newValue }
    }
    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```

_height, _width 프로퍼티는 TwelveOrLess Property Wrapper의 인스턴스를 저장합니다. height와 width 프로퍼티의 getter/setter에서 wrappedValue에 접근하고 있습니다.

## Setting Initial Values for Wrapped Properties
위 예제에서는 래핑된 프로퍼티의 초기값이 TwelveOrLess에 정의된 number의 초기값으로 셋팅되었습니다. 이 Property wrapper(TwelveOrLess)를 사용하는 코드에서는 따로 초기값을 셋팅할 수 없습니다. 예를 들어 SmallRectangle이 정의되는 시점에서는 height나 width의 초기값을 따로 줄 수 없습니다. 초기값(혹은 그 외의 다른 커스터마이징)을 설정하기 위해서는 property wrapper에 이니셜라이저를 추가해야 합니다. TwelveOrLess를 확장한 버전인 SmallNumber를 봅시다. wrappedValue(초기값)와 max값을 설정할 수 있는 이니셜라이저가 정의되어있습니다.

```Swift
@propertyWrapper
struct SmallNumber {
    private var maximum: Int
    private var number: Int

    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, maximum) }
    }

    init() {
        maximum = 12
        number = 0
    }
    init(wrappedValue: Int) {
        maximum = 12
        number = min(wrappedValue, maximum)
    }
    init(wrappedValue: Int, maximum: Int) {
        self.maximum = maximum
        number = min(wrappedValue, maximum)
    }
}
```

프로퍼티에 이 SmallNumber wrapper를 적용할 때는 초기값을 따로 명시하지 않아도, 알아서 SmallNumber의 init() 이니셜라이저를 통해 wrapper가 setup됩니다. 예시를 보겠습니다.

```Swift
struct ZeroRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int
}

var zeroRectangle = ZeroRectangle()
print(zeroRectangle.height, zeroRectangle.width)
// Prints "0 0"
```
SmallNumber()의 호출을 통해 SmallNumber(실제 height와 width값을 감싸고 있는 구조체)의 인스턴스가 생성됩니다. 이니셜라이저의 내부에서는 wrappedValue와 max값의 초기값이 각각 셋팅됩니다. 이 경우에는 init() 안에서 0과 12로 셋팅됩니다. 이 SmallNumber가 기존의 TwelveOrLess와 다른 점은 초기값을 프로퍼티의 선언부에서 셋팅할 수 있도록 해준다는 것입니다. (위 예제에서는 초기값을 따로 명시하지 않아도 기존의 TwelveOrLess처럼 초기값을 알아서 제공해준다는 것을 보여주고 있습니다.)   
프로퍼티의 초기값(wrappedValue)을 따로 명시하는 경우에는 init(wrappedValue:) 이니셜라이저가 사용됩니다. 이것도 예제를 보겠습니다.

```Swift
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}

var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width)
// Prints "1 1"
```

프로퍼티 선언부에 작성된 = 1 에 따라 init(wrappedValue:) 이니셜라이저가 호출되게 됩니다. SmallNumber의 인스턴스들(실제 height, width 값을 감싸고 있는)이 SmallNumber(wrappedValue: 1)의 호출을 통해 생성된다는 뜻입니다. 이 이니셜라이저는 넘겨받은 값을 wrappedValue로 사용하고, 12를 default max값으로 사용합니다.    
프로퍼티 선언 시 커스텀 attribute 뒤에 괄호를 열어 인자들을 넘기면 이 인자들에 부합하는 이니셜라이저로 wrapper가 생성됩니다. 예를 들어, SmallNumber 프로퍼티를 선언할 때 초기값(wrappedValue)과 max값을 전부 제공한다면 init(wrappedValue:maximum:) 이니셜라이저가 사용됩니다.

```Swift
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}

var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width)
// Prints "2 3"

narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width)
// Prints "5 4"
```

property wrapper에 인자를 포함시키면 인스턴스 생성시점에서 wrapper의 초기상태를 설정하거나, 별도의 옵션을 넘길 수도 있습니다. 이 문법은 property wrapper를 사용하는 가장 제너럴한 방법입니다. 필요하면 어떤 인자라도 attribute에 넘길 수 있으며, 이 인자는 이니셜라이저로 전달됩니다. (물론 이것을 처리할 수 있는 이니셜라이저를 만들어야 합니다)

Property wrapper에 인자를 포함하면서, 동시에 초기값을 할당하는 것도 가능합니다. 초기값 할당은 곧 wrappedValue 인자로 취급되고, 이것을 적용할 수 있는 이니셜라이저가 사용됩니다. 예제를 보겠습니다.

```Swift
struct MixedRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber(maximum: 9) var width: Int = 2
}

var mixedRectangle = MixedRectangle()
print(mixedRectangle.height)
// Prints "1"

mixedRectangle.height = 20
print(mixedRectangle.height)
// Prints "12"
```

## Projecting a Value From a Property Wrapper
Property wrapper에는 wrappedValue 외에도 ProjectedValue을 정의하여 추가적인 기능을 제공할 수 있습니다. projectedValue에 접근하기 위해서는 프로퍼티 이름 앞에 달러 기호($)를 붙입니다(그냥 접근하면 wrappedValue에 접근, $기호를 붙이면 projectedValue에 접근하게 됩니다.). 개발자가 직접 작성하는 코드에서는 $로 시작하는 프로퍼티를 정의할 수 없기 때문에, 이 값은 다른 프로퍼티와 간섭될 여지(이름 중복 등)가 없습니다.
위의 SmallNumber 예제에서는 프로퍼티에 너무 큰 수를 할당하려 시도할 경우 property wrapper가 자체적으로 조정을 거쳐서 값을 저장했습니다(max값보다 클 경우 max값을 셋팅). 이렇게 새로운 값을 저장하기 전에 값의 조정이 일어났는지의 여부를 기록하기 위해 SmallNumber 구조체에 projectedValue 프로퍼티를 추가해보겠습니다.

```Swift
@propertyWrapper
struct SmallNumber {
    private var number: Int
    private(set) var projectedValue: Bool

    var wrappedValue: Int {
        get { return number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }

    init() {
        self.number = 0
        self.projectedValue = false
    }
}
struct SomeStructure {
    @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber)
// Prints "false"

someStructure.someNumber = 55
print(someStructure.$someNumber)
// Prints "true"
```

s.\$someNumber는 wrapper의 projectedValue에 접근합니다. 작은 수(4)를 저장하면 값의 조정이 이루어지지 않기 때문에 s.\$someNumber는 false가 됩니다. 그러나 큰 수(55)를 저장하려고 시도했을 때는 값의 조정이 이루어지기 때문에 true가 됩니다.    
projectedValue는 어떤 타입이라도 될 수 있습니다. 예제에서는 오직 하나의 정보(수의 조정이 이루어졌는지의 여부)만을 노출하면 되었으니 Bool 타입을 projectedValue로 사용했습니다. 더 많은 정보를 노출하고 싶다면 다른 데이터 타입을 사용할 수 있으며, self를 사용할 수도 있습니다. 이 경우에는 wrapper 인스턴스 자체가 리턴됩니다.       
프로퍼티 getter 또는 인스턴스 메소드같이 타입을 정의하는 코드 안에서는 projectedValue에 접근할 때 self. 를 생략할 수 있습니다(self.프로퍼티명 대신 프로퍼티명만 써도 된다는 뜻입니다. 일반적인 프로퍼티 접근방식과 동일한 부분입니다.). 아래 예제 코드에서 이 부분을 확인해보세요.

```Swift
enum Size {
    case small, large
}

struct SizedRectangle {
    @SmallNumber var height: Int
    @SmallNumber var width: Int

    mutating func resize(to size: Size) -> Bool {
        switch size {
        case .small:
            height = 10
            width = 20
        case .large:
            height = 100
            width = 100
        }
        return $height || $width
    }
}
```