# Swift5.7

## Unwrapping Optionals Gets an Improvement
기존에 옵셔널 바인딩을 통해 언래핑시에 쉐도잉 작업이 필요했다.    
쉐도잉이란 if문이나 guard문에서 사용될 프로퍼티에 옵셔널한 값을 추출해서 넣어주는 것을 의미한다.
if let x = x {}     
이렇게 사용하는 게 큰 문제는 되지 않지만 만약 프로퍼티의 네이밍이 길 때 if문이 되게 길어질 수 있을 수 있다.   
그걸 위해서 네이밍을 간단하게 한다면 기존의 프로퍼티의 의미를 모호하게 만들 수 있는 문제가 발생한다.   
이번에 Swift5.7에서 도입된 것을 사용하면 쉐도잉 없이 언래핑이 가능하다
```Swift
var name: String? = "abc"
if let name {
    print(name)
}
```

## Multi-statement closure type inference
클로저내에서 반환타입을 명시적으로 적을 필요가 없어졌다.    
기존엔 클로저에서 다른타입을 반환할 경우 명시적으로 반환타입을 적었지만 자동적으로 타입추론이 가능해졌다.

```Swift

//before
let ages = [10, 20, 30, 40, 50]

let ages = ages.map { age -> String in 
  return "Green Age is \(age)"
}

//current
let ages = [10, 20, 30, 40, 50]

let ages = ages.map { age in 
  return "Green Age is \(age)"
}
```

그리고 타입 추론의 성능이 많이 향샹됐다고 wwdc2022에서 발표됐다.