# Structures and Classes

Struct 및 Class는 프로그램 코드의 구성 요소가 되는 범용적이고 유연한 구성 요소입니다. 상수, 변수 및 함수를 정의할 때 사용하는 것과 동일한 구문을 사용하여 구조 및 클래스에 기능을 추가하는 Property 및 Method를 정의합니다.

## Structures and Classes비교
Swift의 Struct와 Class에는 많은 공통점이 있습니다. 둘 다 다음을 수행할 수 있습니다.
* 값을 저장할 Property 정의
* 기능을 제공하는 메소드 정의
* subscript 문법을 사용하여 값에 대한 액세스를 제공하도록 subscript를 정의합니다.
* 초기 상태를 설정하기 위한 이니셜라이저 정의
* 기본 구현 이상으로 기능을 확장하도록 확장(extension)
* 특정 종류의 표준 기능을 제공하는 프로토콜 준수

구조체로는 가능하지 않고 클래스만 가능한 기능은 아래와 같습니다.
* 상속 (Inheritance) : 클래스의 여러 특성을 다른 클래스에 물려 줌
* 타입 캐스팅 (Type casting) : 런타임에 클래스 인스턴스의 타입을 확인하고 해석
* 소멸자 (Deinitializers) : 할당된 자원을 해제(free up) 시킴
* 참조 카운트 (Reference counting) : 클래스 인스턴스에 하나 이상의 참조가 가능

> 구조체는 다른 코드로 전달될 때 항상 복사돼서 전달되고, 참조 카운트(Reference counting)를 사용하지 않습니다.

## Structures and Enumerations Are Value Types

값 타입이라는 뜻은, 이것이 함수에서 상수나 변수에 전달될 때 그 값이 복사되서 전달 된다는 의미입니다. Swift에서 모든 구조체와 열거형 타입은 값 타입입니다. 예제를 보시겠습니다.

```Swift
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd

```
![sharedStateStruct_2x](https://user-images.githubusercontent.com/83381672/178515128-b1717d91-aebf-4269-a3f7-d2220f01e305.png)

## Classes Are Reference Types
값 타입과 달리 참조 타입은 변수나 상수에 값을 할당을 하거나 함수에 인자로 전달할 때 그 값이 복사되지 않고 참조 됩니다. 참조된다는 의미는 그 값을 갖고 있는 메모리를 바라보고 있다는 뜻 입니다. 예제를 보겠습니다.

```Swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0

let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```
![sharedStateClass_2x](https://user-images.githubusercontent.com/83381672/178515232-f10a9407-6f5c-49b9-894b-5b502f45f0bf.png)

