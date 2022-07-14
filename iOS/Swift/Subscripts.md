# Subscripts

클래스, 구조체 그리고 열거형에서 subscripts를 정의해 사용할 수 있습니다. subscripts란 콜렉션, 리스트, 시퀀스 등 집합의 특정 멤버 엘리먼트에 간단하게 접근할 수 있는 문법입니다. subscripts를 이용하면 추가적인 메소드 없이 특정 값을 할당(assign)하거나 가져올 수(retrieve) 있습니다. 예를들면, 배열(Array) 인스턴스의 특정 엘리먼트는 someArray[index]문법으로, Dictionary 인스턴스의 특정 엘리먼트는 someDictionary[key]로 접근할 수 있습니다. 하나의 타입에 여러 서브스크립트를 정의할 수 있고 오버로드(Overload)도 가능합니다. 뿐만아니라 단일 인자 값을 넘어, 필요 따라 복수 인자 값을 사용할 수 있습니다.

## Subscript Syntax

서브스크립트 선언 문법은 instance method와 computed property를 선언하는 것과 비슷합니다. instance method와 다른 점은, 서브스크립트는 읽고-쓰기(read-write) 혹은 읽기 전용(read only)만 가능하다는 것입니다. 정의는 computed property 방식과 같이 setter, getter 방식을 따릅니다.

```Swift
subscript(index: Int) -> Int {
    get {
        // 적절한 반환 값
    }
    set(newValue) {
        // 적절한 set 액션
    }
}
```

서브스크립트의 set에 대한 인자 값을 따로 지정하지 않으면 기본 값(default value)로 newValue를 사용합니다. 읽기 전용으로 선언하려면 get, set을 지우고 따로 지정하지 않으면 get으로 동작하게 되서 읽기 전용으로 선언됩니다.

```Swift
subscript(index: Int) -> Int {
    // 적절한 반환 값
}
```

다음은 읽기 전용으로 선언한 서브스크립트의 (예)입니다.

```Swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// "six times three is 18" 출력
```

## Subscript Usage

```Swift
var numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
numberOfLegs["bird"] = 2
```
numberOfLegs값은 타입 추론에 의해 [String: Int]형을 갖습니다. numberOfLegs["bird"] = 2는 Dictionary 변수 numberOfLegs에 "bird"의 문자열 키와 2의 Int 값을 추가하는 서브스크립트 문법입니다.

## Subscript Options
서브스크립트는 입력 인자의 숫자에 제한이 없고, 입력 인자의 타입과 반환 타입의 제한도 없습니다. 다만 in-out 인자(in-out parameter)나 기본 인자 값(default parameter value)을 제공할 수는 없습니다. 서브스크립트는 오버로딩도 허용합니다. 그래서 인자형, 반환형에 따라 원하는 수 만큼의 서브스크립트를 선언할 수 있습니다. 다음은 서브스크립트를 이용해 다차원 행열을 선언하고 접근하는 (예) 입니다.

```Swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }
    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}

let matrix = Matrix(rows: 3, columns: 3)
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2
```