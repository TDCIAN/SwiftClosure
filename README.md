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

