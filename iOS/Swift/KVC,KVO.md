# KVC, KVO

## Key란?
Key Value Coding, Key Value Observing에서 말하는 Key는 문자열!    
Key값을 통해서 인스턴스의 프로퍼티를 간접적으로 접근하여 값을 설정하거나 가져올 수 있는 Objective-C에서 나온 개념이다.

## KVC(Key Value Coding)
* 인스턴스의 프로퍼티에 접근할 때 Key값인 문자열로 접근하는 방법
* KVC는 Objective-C 런타임에 의존하므로 프로퍼티 앞에 @objc 어노테이션을 붙여서 사용
* 단, Objective-C의 것이기 때문에 NSObject가 가지고 있으므로 NSObject의 서브클래스여야 가능

```Swift
class People: NSObject {
    @objc var name: String?
    @objc var age: Int?
    @objc var job: String?
}

//일반적으로 프로퍼티에 접근하는 방법
let zezero = People()
zezero.name = "제제로"
zezero.age = 26
zezero.job = "iOS Developer"

//Key값을 통해 간접적으로 접근하는 방법
zezero.value(forKey: "name") // 제제로
zezero.setValue("신재영", forKey: "name")

//KeyPath를 통해 간접적으로 접근하는 방법
zezero[keyPath: \People.age] //26
```

## KVO(Key Value Observing)
* Key값을 가지고 프로퍼티에 접근하여 Observing할 수 있는 방법
* 객체의 프로퍼티의 변경사항을 다른 객체에 알리기 위해 사용하는 코코아 프로그래밍 패턴
* 프로퍼티 앞에 @objc dynamic 를 붙여서 사용
* Observing도 NSObject의 서브클래스여야 가능

```Swift
class Person2: NSObject { // NSObject 서브클래싱
  @objc dynamic var name: String? // @objc dynamic 어노테이션
}

let zezero = Person2()
zezero.observe(
    \.name,
    options: [.old, .new]
) {
    instance, change in
    print(change.oldValue, change.newValue)
}
zezero.name = "제제로" //Optional(nil) Optional(Optional("제제로"))
```

### NSKeyValueObservingOptions
* old
* new
* initial
* prior


initial은 초기시화시에도 Observer handler를 호출할거냐 라고 보면 된다.

```Swift
let zezero = Person2(name: "신재영")
zezero.observe(\.name, options: [.old, .new, .initial]) { instance, change in
    print(change.oldValue, change.newValue)
    //Optional(nil) Optional(Optional("신재영"))
    //Optional(Optional("신재영")) Optional(Optional("제제로"))
}
zezero.name = "제제로" 
```

prior은 이전의 상태와 지금의 상태를 전부 호출하는 옵션이라고 볼 수 있다.
```Swift
let zezero = Person2()
zezero.observe(\.name, options: [.old, .new, .prior]) { instance, change in
    print(change.oldValue, change.newValue)
    //Optional(nil) Optional(nil)
    //Optional(nil) Optional(Optional("제제로"))
}
zezero.name = "제제로" 
```

## KVO의 장단점

### 장점 
   * willSet과 didSet과 사용법이 비슷한데 KVO장점은 객체 외부에서 추가하여 사용하는 것이므로 객체의 구현을 변경하지 않고 내구 객체의 상태 변화에 대응 할 수 있음
   * KeyPath를 사용하여 nested프로퍼티도 관찰 가능
   * Model과 View같이 논리적으로 분리된 파트간의 변경사항을 전달하여 동기화가 가능하다


### 단점
* NSObject 상속해야한다 -> Objective-C 런타임에 의존하게 됨 
* dynamic을 붙힘으로써 dynamic dispatch를 사용해 성능적 저하 이슈 발생가능성 있음