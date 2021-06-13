# SwiftClosure

- 출처: 야곰의 스위프트 프로그래밍

## 클로저
- 스위프트에서 함수형 프로그래밍 패러다임을 접할 때 첫걸음으로 꼭 알아야 할 녀석이 바로 클로저입니다.
- 클로저를 잘 이해해야 스위프트의 함수형 프로그래밍 패러다임 스타일을 좀 더 명확하게 이해할 수 있습니다
- 클로저와 제네릭(Generics), 프로토콜(Protocol), 모나드(Monad) 등이 결합해서 스위프트는 훨씬 강력한 언어가 되었습니다.
- 클로저는 일정 기능을 하는 코드를 하나의 블록으로 모아놓은 것을 말합니다.
- 함수는 클로저의 한 형태입니다.
- 클로저는 변수나 상수가 선언된 위치에서 참조(Reference)를 획득(Caputre)하고 저장할 수 있습니다.
- 이를 변수나 상수의 클로징(잠금)이라고 하며 클로저는 여기서 착안된 이름입니다.
- 획득 때문에 메모리에 부담이 가지 않을까 걱정할 수도 있지만, 앞서 설명했듯이 스위프트는 스스로 메모리를 관리합니다.
- 클로저는 세 가지 형태가 있습니다
  - 이름이 있으면서 어떤 값도 획득하지 않는 전역함수의 형태
  - 이름이 있으면서 다른 함수 내부의 값을 획득할 수 있는 중첩된 함수의 형태
  - 이름이 없고 주변 문맥에 따라 값을 획득할 수 있는 축약 문법으로 작성한 형태

* 클로저의 활용 예시
- 클로저는 매개변수와 반환 값의 타입을 문맥을 통해 유추할 수 있기 때문에 매개변수와 반환 값의 타입을 생략할 수 있습니다.
- 클로저에 단 한 줄의 표현만 들어있다면 암시적으로 이를 반환 값으로 취급합니다.
- 축약된 전달인자 이름을 사용할 수 있습니다.
- 후행 클로저 문법을 사용할 수 있습니다.

- 클로저 표현 방법은 클로저가 함수의 모습이 아닌 하나의 블록의 모습으로 표현될 수 있는 방법을 의미합니다.
- 클로저 표현 방법은 클로저의 위치를 기준으로 크게 기본 클로저 표현과 후행 클로저 표현이 있습니다.
- 또, 각 표현 내에서 가독성을 해치지 않는 선에서 표현을 생략하거나 축약할 수 있는 방법이 있습니다.

### 기본 클로저
- (코드) 스위프트 라이브러리의 sorted(by:) 메서드 정의
```swift
public func sorted(by areaIncreaseingOrder: (Element, Element) -> Bool) -> [Element]
```
- String 타입의 배열에 이름을 넣어 영문 알파벳을 내림차순으로 정렬하려고 합니다.
- (코드) 정렬에 사용될 이름 배열
```swift
let names: [String] = ["wizplan", "eric", "yagom", "jenny"]
```
- sorted(by:) 메서드는 (배열의 타입과 같은 두 개의 매개변수를 가지며 Bool 타입을 반환하는) 클로저를 전달인자로 받을 수 있습니다.
- 반환하는 Bool 값은 첫 번째 전달인자 값이 새로 생성되는 배열에서 두 번째 전달인자 값보다 먼저 배치되어야 하는지에 대한 결과값입니다.
- true를 반환하면 첫 번째 전달인자가 두 번째 전달인자보다 앞에 옵니다
- (코드) 정렬을 위한 함수 전달
```swift
func backwards(first: String, second: String) -> Bool {
  print("\(first) \(second) 비교중")
  return first > second
}

let reversed: [String] = names.sorted(by: backwards)
print(reversed) // ["yagom", "wizplan", "jenny", "eric"]
```
- 만약 first 문자열이 second 문자열보다 크다면('알파벳이 더 뒤쪽이다'라는 뜻입니다. 예를 들어 'B'는 'A'보다 큽니다. 'yagom'은 'eric'보다 큽니다.) backwards(first: second:) 함수의 반환 값은 true가 될 것입니다.
- 즉, 값이 더 큰 first 문자열이 second 문자열보다 앞쪽에 정렬되어야 한다는 뜻입니다.
- 그러나 first > second라는 반환 값을 받기 위해 너무 많은 표현을 사용했습니다.
- 예시 코드에서 print() 함수는 참고용 콘솔 출력이니 제외해도 역시 많습니다.
- 함수 이름부터 매개변수 표현까지 부가적인 표현도 많습니다.
- 이를 클로저 표현을 사용해서 조금 더 간결하게 표현하겠습니다.
- 클로저 표현은 통상 아래 형식을 따릅니다.
- (코드) 기본 클로저 표현 형식
```swift
{ (매개변수들) -> 반환 타입 in
    실행 코드
}
```
- 클로저도 함수와 마찬가지로 입출력 매개변수를 사용할 수 있습니다.
- 매개변수 이름을 지정한다면 가변 매개변수 또한 사용 가능합니다.
- 다만 클로저는 매개변수 기본값을 사용할 수 없습니다.
- (코드) backwards(first: second:) 함수를 클로저 표현으로 대체하여 sorted(by:) 메서드에 클로저 전달
```swift
// backwards(first: second:) 함수 대신에 sorted(by:) 메서드의 전달인자로 클로저를 직접 전달합니다.
let reversed: [String] = names.sorted (by: {first: String, second: String} -> Bool in
    return first > second
})
print(reversed) // ["yagom", "wizplan", "jenny", "eric"]
```
- sorted(by:) 메서드로 전달하는 클로저의 매개변수 개수와 타입, 그리고 반환 타입이 모두 backwards(first: second:) 함수와 같습니다.
- 이렇게 프로그래밍하면 sorted(by:) 메서드로 전달되는 backward(first: second:) 함수가 어디에 있는지, 어떻게 구현되어 있는지 찾아다니지 않아도 됩니다.
- 물론, 반복해서 같은 기능을 사용하려면 함수로 구현해두는 것도 나쁘지 않습니다. 이는 여러분의 선택입니다.

### 탈출 클로저
- 함수의 전달인자로 전달한 클로저가 함수 종료 후에 호출될 때 클로저가 탈출(Escape)한다고 표현합니다.
- 클로저를 매개변수로 갖는 함수를 선언할 때 매개변수 이름의 콜론(:) 뒤에 @escaping 키워드를 사용하여 클로저가 탈출한다는 것을 허용한다고 명시해줄 수 있습니다.
- 예를 들어 비동기 작업을 실행하는 함수들은 클로저를 컴플리션 핸들러(Completion handler) 전달인자로 받아옵니다.
- 비동기 작업으로 함수가 종료되고 난 후 호출할 필요가 있는 클로저를 사용해야 할 때 탈출 클로저(Escaping Closure)가 필요합니다.
- @escaping 키워드를 따로 명시하지 않는다면 매개변수로 사용되는 클로저는 기본으로 비탈출 클로저입니다. 
- 함수로 전달된 클로저가 함수의 동작이 끝난 후 사용할 필요가 없을 때 비탈출 클로저를 사용합니다.
- 클로저가 함수를 탈출할 수 있는 경우 중 하나는 함수 외부에 정의된 변수나 상수에 저장되어 함수가 종료된 후에 사용할 경우입니다.
- 예를 들어 비동기로 작업을 하기 위해서 컴플리션 핸들러를 전달인자를 이용해 클로저 형태로 받는 함수들이 많습니다.
- 함수가 작업을 종료하고 난 이후(즉, 함수의 return 후)에 컴플리션 핸들러, 즉 클로저를 호출하기 때문에 클로저는 함수를 탈출해 있어야만 합니다.
- 함수의 전달인자로 전달받은 클로저를 다시 반환할 때도 마찬가지입니다.
- (코드) 탈출 클로저와 그 클로저를 저장할 수 있는 함수 외부의 배열 변수
```swift
var completionHandlers: [() -> Void] = []

func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
  completionHandlers.append(completionHandler)
}
```
- (코드) 함수를 탈출하는 클로저의 예
```swift
typealias VoidVoidClosure = () -> Void

let firstClosure: VoidVoidClosure = {
  print("Closure A")
}

let secondClosure: VoidVoidClosure = {
  print("Closure B")
}

// first와 second 매개변수 클로저는 함수의 반환 값으로 사용될 수 있으므로 탈출 클로저입니다.
func returnOneClosure(first: @escaping VoidVoidClosure, second: @escaping VoidVoidClosure, shouldReturnFirstClosure: Bool) -> VoidVoidClosure {
  // 전달인자로 전달받은 클로저를 함수 외부로 다시 반환하기 때문에 함수를 탈출하는 클로저입니다.
  return shouldReturnFirstClosure ? first : second
}

// 함수에서 반환한 클로저가 함수 외부의 상수에 저장되었습니다.
let returnedClosure: VoidVoidClosure = returnOneClosure(first: firstClosure, second: secondClosure, shouldReturnFirstClosure: true)

returnedClosure() // Closure A

var closures: [VoidVoidClosure] = []

// closure 매개변수 클로저는 함수 외부의 변수에 저장될 수 있으므로 탈출 클로저입니다.
func appendClosure(closure: @escaping VoidVoidClosure) {
  // 전달인자로 전달받은 클로저가 함수 외부의 변수 내부에 저장되므로 함수를 탈출합니다.
  closures.append(closure)
}
```
- 위 코드에서 두 함수의 전달인자로 전달하는 클로저 앞에 @escaping 키워드를 사용하여 탈출클로저임을 명시해주어야 합니다.
- 이 코드는 클로저 모두가 탈출할 수 있는 조건이 명확하기 때문에 @escaping 키워드를 사용하여 탈출 클로저임을 명시하지 않으면 컴파일 오류가 발생합니다.
- 이 코드는 함수 외부로 다시 전달되어 외부에서 사용이 가능하다든가, 외부 변수에 저장되는 등 클로저의 탈출 조건을 모두 갖추고 있습니다.
- 타입 내부 메서드의 매개변수 클로저에 @escaping 키워드를 사용하여 탈출 클로저임을 명시한 경우, 클로저 내부에서 해당 타입의 프로퍼티나 메서드, 서브스크립트 등에 접근하려면 self 키워드를 명시적으로 사용해야 합니다.
- 비탈출 클로저 내부에서 타입 내부의 프로퍼티나 메서드, 서브스크립트 등에 접근할 때 self 키워드를 꼭 써주지 않아도 됩니다.
- 즉, 비탈출 클로저 내부에서 self 키워드는 선택 사항입니다.
- (코드) 클래스 인스턴스 메서드에 사용되는 탈출, 비탈출 클로저
```swift
typealias VoidVoidClosure = () -> Void

func functionWithNoEscapeClosure(closure: VoidVoidClosure) {
  closure()
}

func functionWithEscapingClosure(completionHandler: @escaping VoidVoidClosure) -> VoidVoidClosure {
  return completionHandler
}

class SomeClass {
  var x = 10
  
  func runNoEscapeClosure() {
    // 비탈출 클로저에서 self 키워드 사용은 선택 사항입니다.
    functionWithNoEscapeClosure { x = 200 }
  }
  
  func runEscapingClosure() -> VoidVoidClosure {
    // 탈출 클로저에는 명시적으로 self를 사용해야 합니다.
    return functionWithEscapingClosure { self.x = 100 }
  }
}

let instance: SomeClass = SomeClass()
instance.runNoEscapeClosure()
print(instance.x) // 200

let returnedClosure: VoidVoidClosure = instance.runEscapingClosure()
returnedClosure()
print(instance.x) // 100

```
- 위 코드에서 비탈출 클로저에서는 인스턴스의 프로퍼티인 x를 사용하기 위해 self 키워드를 생략해도 무관했지만, 탈출하는 클로저에서는 값 획득을 하기 위해 self 키워드를 사용하여 프로퍼티에 접근해야만 합니다.

### 후행 클로저
- 함수나 메서드의 마지막 전달인자로 위치하는 클로저는 함수나 메서드의 소괄호를 닫은 후 작성해도 됩니다.
- 클로저가 조금 길어지거나 가독성이 조금 떨어진다 싶으면 후행 클로저(Trailing Closure) 기능을 사용하면 좋습니다.
- Xcode에서 자동완성 기능을 사용하면 자동으로 후행 클로저로 유도합니다.
- 단, 후행 클로저는 맨 마지막 전달인자로 전달되는 클로저에만 해당되므로 전달인자로 클로저 여러 개를 전달할 때는 맨 마지막 클로저만 후행 클로저로 사용할 수 ㅇㅆ습니다.
- 또한 sorted(by:) 메서드처럼 단 하나의 클로저만 전달인자로 전달하는 경우에는 소괄호를 생략해줄 수도 있습니다.
- (코드) 후행 클로저 표현
```swift
// 후행 클로저의 사용
let reversed: [String] = names.sorted() { (first: String, second: String) -> Bool in
    return first > second
}

// sorted(by:) 메서드의 소괄호까지 생략 가능합니다.
let reversed: [String] = names.sorted { (first: String, second: String) -> Bool in
    return first > second
}
```


### 클로저 표현 간소화

#### 1. 문맥을 이용한 타입 유추
- 메서드의 전달인자로 전달하는 클로저는 메서드에서 요구하는 형태로 전달해야 합니다.
- 즉, 매개변수의 타입이나 개수, 반환 타입 등이 같아야 전달인자로서 전달할 수 있습니다.
- 이를 다르게 말하면, 전달인자로 전달할 클로저는 이미 적합한 타입을 준수하고 있다고 유추할 수 있습니다.
- 문맥에 따라 적절히 타입을 유추할 수 있기 때문에 전달인자로 전달하는 클로저를 구현할 때는 매개변수의 타입이나 반환 값의 타입을 굳이 표현해주지 않고 생략하더라도 문제가 없습니다.
- (코드) 클로저의 타입 유추
```swift
// 클로저의 매개변수 타입과 반환 타입을 생략하여 표현할 수 있습니다.
let reversed: [String] = names.sorted { (first, second) in
    return first > second
}
```

#### 2. 단축 인자 이름
- 스위프트는 매개변수의 이름을 생략할 수 있도록 단축 인자 이름을 제공합니다.
- 단축 인자 이름은 첫 번째 전달인자부터 $0, $1, $2, $3, ... 순서로 $와 숫자의 조합으로 표현합니다.
- 단축 인자 표현을 사용하게 되면 매개변수 및 반환 타입과 실행 코드를 구분하기 위해 사용했던 키워드 in을 사용할 필요도 없어집니다.
- (코드) 단축 인자 이름 사용
```swift
// 단축 인자 이름 사용
let reversed: [String] = names.sorted {
    return $0 > $1
}
```

#### 3. 암시적 반환 표현
- 클로저에서는 return 키워드마저 생략할 수 있습니다.
- 만약 클로저가 반환 값을 갖는 클로저이고 클로저 내부의 실행문이 단 한 줄이라면, 암시적으로 그 실행문을 반환 값으로 사용할 수 있습니다.
- (코드) 암시적 반환 표현의 사용
```swift
// 암시적 반환 표현의 사용
let reversed: [String] = names.sorted { $0 > $1 }
```

#### 4. 연산자 함수
- 클로저는 매개변수의 타입과 반환 타입이 연산자를 구현한 함수의 모양과 동일하다면 연산자만 표기하더라도 알아서 연산하고 반환합니다.
- (코드) > 연산자 정의
```swift
public func > <T: Comparable>(lhs: T, rhs: T) -> Bool
```
- 위 코드에서는 > 자체가 함수의 이름입니다.
- 이 함수는 우리가 전달인자로 보내기에 충분한 조건을 갖고 있습니다.
- (코드) 클로저로서의 연산자 함수 사용
```swift
// 연산자 함수를 클로저의 역할로 사용
let reversed: [String] = names.sorted(by: >)
```


### 값 획득
- 클로저는 자신이 정의된 위치의 주변 문맥을 통해 상수나 변수를 획득(Capture)할 수 있습니다.
- 값 획득을 통해 클로저는 주변에 정의한 상수나 변수가 더 이상 존재하지 않더라도 해당 상수나 변수의 값을 자신 내부에서 참조하거나 수정할 수 있습니다.
- 이 이야기를 하는 이유는 클로저가 비동기 작업에 많이 사용되기 때문입니다.
- 클로저를 통해 비동기 콜백(Call-back)을 작성하는 경우, 현재 상태를 미리 획득해두지 않으면, 실제로 클로저의 기능을 실행하는 순간에는 주변의 상수나 변수가 이미 메모리에 존재하지 않는 경우가 발생합니다.
- 중첩 함수도 하나의 클로저 형태라고 앞에서 설명했는데, 이 중첩 함수 주변의 변수나 상수를 획득해 놓을 수도 있습니다.
- 즉, 자신을 포함하는 함수의 지역변수나 지역상수를 획득할 수 있습니다.
- (코드) incrementer라는 함수를 중첩 함수로 포함하는 makeIncrementer 함수
```swift
func makeIncrementer(forIncrement amount: Int) -> (() -> Int) {
  var runningTotal = 0
  func incrementer() -> Int {
      runningTotal += amount
      return runningTotal
  }
  return incrementer
}
```
- 중첩 함수인 incrementer() 함수는 자신 주변에 있는 runningTotal과 amount라는 두 값을 획득합니다.
- 두 값을 획득한 후에 incrementer는 클로저로서 makeIncrementer 함수에 의해 반환됩니다.
- makeIncrementer 함수의 반환 타입은 () -> Int입니다.
- 이는 함수객체를 반환한다는 의미입니다.
- 반환하는 함수는 매개변수를 받지 않고 반환 타입은 Int인 함수로, 호출할 때마다 Int 타입의 값을 반환해줍니다.
- incrementer가 반환하게 될 값을 저장하는 용도로 runningTotal을 정의했고, 0으로 초기화해두었습니다.
- 그리고 forIncrement라는 전달인자 레이블과 amount라는 매개변수 이름이 있는 Int 타입 매개변수 하나가 있습니다.
- incrementer() 함수가 호출될 때마다 amount의 값만큼 runningTotal 값이 증가합니다.
- 또한 값을 증가시키는 역할을 하는 incrementer라는 이름의 중첩 함수를 정의했습니다.
- 이 incrementer() 함수는 amount 값을 runningTotal에 대하여 결과값을 반환합니다.
- (코드) incrementer() 함수
```swift
func incrementer() -> Int {
  runningTotal += amount
  return runningTotal
}
```
- incrementer() 함수는 어떤 매개변수도 갖지 않으며, runningTotal이라는 변수가 어디 있는지 찾아볼 수도 없습니다. 지금 이 형태만으로는 잘못된 코드입니다.
- 그러나 incrementer() 함수 주변에 runningTotal과 amount 변수가 있다면 incrementer() 함수는 두 변수의 참조를 획득할 수 있습니다.
- 참조를 획득하면 runningTotal과 amount는 makeIncrementer 함수의 실행이 끝나도 사라지지 않습니다.
- 게다가 incrementer가 호출될 때마다 계속해서 사용할 수 있습니다.
- makeIncrementer(forIncrement:) 함수를 사용하여 incrementByTwo라는 이름의 상수에 increment 함수를 할당해줬습니다.
- incrementByTwo를 호출할 때마다 runningTotal은 값이 2씩 증가합니다.
- (코드) incrementByTwo 상수에 함수 할당
```swift
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)

let first: Int = incrementByTwo() // 2
let second: Int = incrementByTwo() // 4
let third: Int = incrementByTwo() // 6
```
- incrementer를 하나 더 생성해주면, incrementerByTwo와는 별개의 다른 참조를 갖는 runningTotal 변숫값을 확인할 수 있습니다.
- (코드) 각각의 incrementer의 동작
```swift
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let incrementByTwo2: (() -> Int) = makeIncrementer(forIncrement: 2)
let incrementByTen: (() -> Int) = makeIncrementer(forIncrement: 10)

let first: Int = incrementByTwo() // 2
let second: Int = incrementByTwo() // 4
let third: Int = incrementByTwo() // 6

let first2: Int incrementByTwo2() // 2
let second2: Int incrementByTwo2() // 4
let third2: Int incrementByTwo2() // 6

let ten: Int = incrementByTen() // 10
let twenty: Int = incrementByTen() // 20
let thirty: Int = incrementByTen() // 30
```
- 각각의 incrementer 함수는 언제 호출이 되더라도 자신만의 runningTotal 변수를 갖고 카운트하게 됩니다.
- 다른 함수의 영향도 전혀 받지 않습니다.
- 각각 자신만의 runningTotal의 참조를 미리 획득했기 때문입니다.
- ** Note: 클래스 인스턴스 프로퍼티로서의 클로저
  - 클래스 인스턴스의 프로퍼티로 클로저를 할당한다면 클로저는 해당 인스턴스 또는 인스턴스의 멤버의 참조를 획득할 수 있으나, 클로저와 인스턴스 사이에 강한참조 순환 문제가 발생할 수 있습니다.
  - 강한참조 순환 문제는 획득목록을 통해 없앨 수 있습니다.


### 클로저는 참조 타입
- 함수나 클로저를 상수나 변수에 할당할 때마다 사실은 상수나 변수에 함수나 클로저의 참조를 설정하는 것입니다.
- 즉, incrementByTwo라는 상수에 클로저를 할당한다는 것은 클로저의 내용물, 즉 값을 할당하는 것이 아니라 해당 클로저의 참조를 할당하는 것입니다.
- 결국 클로저의 참조를 다른 상수에 할당해준다면 이는 두 상수가 모두 같은 클로저를 가리킨다는 뜻입니다.
- (코드) 참조 타입인 클로저
```swift
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let sameWithIncrementByTwo: (() -> Int) = incrementByTwo

let first: Int = incrementByTwo() // 2
let second: Int = sameWithIncrementByTwo() // 4
```
