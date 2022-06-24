# ReactorKit - Pulse

ReactorKit을 사용할  때 액션이 발생돼서 새로운 State를 방출될 때 의도치 않은 State까지 방출되는 문제가 발생한다.
이러한 문제를 해결하기 위해 그동안 RxSwift의 DistinctUntilChanged연산자를 사용해 똑같은 값을 방출하는 경우에는 해당 이벤트를 무시하도록 구현했었다. 하지만 예를 들어 State로 에러메시지를 방출하는 경우 똑같은 에러가 두번 발생하여 같은 에러메시지를 두번 방출하는 경우 DistinctUntilChanged연산자를 사용하면 에러메시지가 한번만 출력될 것이다. 이러한 문제를 해결하기 위해 ReactorKit에는 Pulse라는 PropertyWrapper가 존재한다.

## Pulse 
Pulse는 변형(값의 대입)이 있을 때만 diff가 발생한다.
코드를 보면 값은 값을 주입해도 valueUpdatedCount가 증가하는 것을 볼 수 있다

```Swift
var messagePulse: Pulse<String?> = Pulse(wrappedValue: "Hello tokijh")

let oldMessagePulse: Pulse<String?> = messagePulse
messagePulse.value = "Hello tokijh" // add valueUpdatedCount +1

oldMessagePulse.valueUpdatedCount != messagePulse.valueUpdatedCount // true
oldMessagePulse.value == messagePulse.value // true
```

Pulse는 동일한 값이더라도 새로운 값이 할당된 경우에만 이벤트를 수신하고자 할 때 사용한다.         
변형이 있을 때만 방출하고 싶은 State앞에 @Pulse라는 PropertyWrapper를 붙이고 뷰에서 pulse 메소드의 해당 프로퍼티의 KeyPath를 전달하는 것으로 사용할 수 있다.

```Swift
// Reactor
private final class MyReactor: Reactor {
  struct State {
    @Pulse var alertMessage: String?
  }

  func mutate(action: Action) -> Observable<Mutation> {
    switch action {
    case let .alert(message):
      return Observable.just(Mutation.setAlertMessage(message))
    }
  }

  func reduce(state: State, mutation: Mutation) -> State {
    var newState = state

    switch mutation {
    case let .setAlertMessage(alertMessage):
      newState.alertMessage = alertMessage
    }

    return newState
  }
}

// View
reactor.pulse(\.$alertMessage)
  .compactMap { $0 } // filter nil
  .subscribe(onNext: { [weak self] (message: String) in
    self?.showAlert(message)
  })
  .disposed(by: disposeBag)

// Cases
reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
reactor.action.onNext(.doSomeAction)    // showAlert() is not called
reactor.action.onNext(.alert("Hello"))  // showAlert() is called with `Hello`
reactor.action.onNext(.alert("tokijh")) // showAlert() is called with `tokijh`
reactor.action.onNext(.doSomeAction)    // showAlert() is not called
```

pulse메소드 코드를 보면 해당 프로퍼티의 valueUpdatedCount가 변경될 때만 값을 방출하도록 되어있다.
```Swift
extension Reactor {
  public func pulse<Result>(_ transformToPulse: @escaping (State) throws -> Pulse<Result>) -> Observable<Result> {
    return self.state.map(transformToPulse).distinctUntilChanged(\.valueUpdatedCount).map(\.value)
  }
}
```
