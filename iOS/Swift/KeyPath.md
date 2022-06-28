# KeyPath

KeyPath는 값에 대한 참조가 아닌 프로퍼티에 대한 참조이다.
즉, 객체 내부의 값에 직접적으로 참조하는 것이 아니라 값의 이름(프로퍼티)를 참조하는 것이다. 

흔히 객체에 대한 인스턴스를 생성하고 프로퍼티의 값을 가져오기 위해 다음과 같이 사용한다.
``` Swift
class Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

let p = Person(name: "제제로")
print(p.name) // 값에 대한 직접 참조
```

이렇게 값에 접근하는 방식은 직접적으로 참조하는 것이다.   
위에 방식과 다르게 KeyPath를 사용해 값을 간접적으로 가져올 수 있다.

```Swift
let path = \Person.name // KeyPath<Person,String>
print(p[keyPath: path]) //간접 참조
```
KeyPath는 말 그대로 경로로써 참조하고 싶은 프로퍼티에 대한 경로를 프로퍼티 이름으로 나타낸다      
ex) \Person.info.name     
키 경로는 역슬래시( \ ) 와 타입, 마침표( . ) 경로로 구성된다.
![21312](https://user-images.githubusercontent.com/83381672/176203765-838855e0-2b30-4866-964f-6d889dcd9fdb.png)
KeyPath의 첫번째 타입파라미터는 루트에 해당하는 타입이 오고 두 번째 타입 파라미터는 참조하고자 하는 프로퍼티의 타입이 오게 된다.


## KeyPath의 종류
* AntyKeyPath : 타입이 지워진 KeyPath     
* PartialKeyPath : 부분적으로 타입이 지워진 KeyPath     
* KeyPath : 읽기 전용   
* WritableKeyPath : 읽기 및 쓰기 가능    
* ReferenceWritableKeyPath : 클래스의 인스턴스에 사용 가능. 변경 가능한 모든 프로퍼티에 대한 read & write access 제공.

```Swift
struct Product: Decodable {
    let id: Int
    let name: String
    let thumbnail: String
    let description: ProductDescription
    let rate: Double
    var bookmarkTime: Date?
    var isBookmarked: Bool?
    
    func toEntity() -> ProductEntity {
        return ProductEntity(
            id: self.id,
            name: self.name,
            thumbnail: self.thumbnail,
            rate: self.rate,
            desc: self.description.toEntity(),
            bookmarkTime: Date()
        )
    }
}
//위와 같은 모델이 존재할 때

let a = \Product.id // KeyPath<Product, Int>
//프로퍼티가 let으로 선언되어 있기 때문에 읽기전용인 KeyPath타입이다.
        
let b = \WholeListViewController.reactor //ReferenceWritableKeyPath<WholeListViewController, WholeListReactor?>
// vc는 class타입이고 reactor는 var로 선언되어 있기 때문에 ReferenceWritableKeyPath타입이 된다.
let c = \Product.isBookmarked
//프로퍼티가 var로 선언되어 있고 root가 Struct이기 때문에 
//WritableKeyPath<Product, Bool?> 타입이다.
```

## KeyPath의 활용
```Swift
    reactor.pulse(\.$sections)
        .bind(to: favoritesTableView.rx.items(dataSource: FavoritesDataSource.dataSource(delegate: self)))
        .disposed(by: disposeBag)
        
    reactor.state.map(\.sortType) // reactor.state.map { $0.sortType }
        .distinctUntilChanged()
        .bind(onNext: setSortLabel(type:))
        .disposed(by: disposeBag)
```
위와 같이 map함수와 같은 고차함수에서 KeyPath<Root,Value>를 (Root) -> Value 함수 처럼 사용할 수 있다.       
Root를 추론할 수 있는 경우에는 생략이 가능한 것 같다.
