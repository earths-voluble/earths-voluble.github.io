---
title: 옵셔널,바인딩
date: 2023-12-31 23:27:21 +0900
categories: [DEVELOPMENT, SWIFT]
tags: [swift,문법]     # TAG names should always be lowercase
---

## Optional
> A type that represents either a wrapped value or the absence of a value. 
> 값 혹은 비어있는 값을 나타내는, 싸여있는(래핑된)타입
{: .prompt-tip }

### 왜 Optional?
`Optional`이 아닌 타입은 `Non-optional` 타입이라고 한다.

`Non-optional`타입은 반드시 값을 가져야만 한다.
만약 `Non-optional`타입에 값이 없다면, 크래시가 일어난다.
때문에 이와 같은 위험을 줄여 언어의 안전성을 제고하기 위해, `Swift`는 값이 있어도 되고 없어도 되는 `Optional`이라는 타입을 사용한다.
이 `Optional` 타입은 싸여있기 때문에 그냥은 사용할 수 없고, 여러 가지 방법으로 이 래핑을 풀어야 한다.
다음은 `Optional`타입의 래핑을 푸는 몇 가지 방법들이다.

### Optional Binding
- `if let` 바인딩
```swift
let bindMe: Optional<Int> = Int(1)
if let bound = bindMe {
	print(bound)
} // 1
```
이와 같이 `Optional` 값의 래핑을 벗겨서 사용할 수 있으며
```swift
let bindMe: Optional<Int> = Int(1)
if let bindMe = bindMe {
		print(bindMe)
} // 1
```
이렇게 같은 이름을 사용해도 되고,
```swift
let bindMe: Optional<Int> = Int(1)
if let bindMe {
		print(bindMe)
} // 1
```
Swift 5.7부터는 이렇게 `=`연산자를 생략하고 사용할 수도 있다.

- `guard let` 바인딩
`guard let` 바인딩은 함수 내에서만 사용 가능하다. ->
함수의 조건을 검사하는 용도인 guard의 특성상 guard let 역시 함수 내에서만 사용 가능하고, return, throw, break, continue 등의 제어문 전환 명령어가 필요기 때문이다.

```swift
let bindMe: Optional<Int> = Int(1)
func illBindYou(_ who: Optional<Int>) {
    guard let bindMe = bindMe else { return }
    print(bindMe)
}
illBindYou(bindMe) // 1
```

```swift
let bindMe: Optional<Int> = Int(1)
func illBindYou(_ who: Optional<Int>) {
    guard let bindMe else { return }
    print(bindMe)
}
illBindYou(bindMe) // 1
```

`if let` 바인딩과 마찬가지로 `=` 연산자는 생략할 수 있다.

---

### nil-Coalescing
옵셔널 값이 `nil`일 경우를 대비해서 `nil-coalescing` 연산자(`??`)를 사용한다. 
좌항이 `nil`이 아닐 경우 좌항을 할당하고, 좌항이 `nil`일 경우에는 우항을 할당한다.
`nil-Coalescing`을 사용하면 옵셔널 값이 비어있을 경우를 위한 기본값을 설정하기 좋다.

```swift
let nilCoalesceLhs: Optional<Int> = 1
let nilCoalesceRhs: Optional<Int> = nil

let coalesceLhs = nilCoalesceLhs ?? Int(2)
print(coalesceLhs) // 1

let coalesceRhs = nilCoalesceRhs ?? Int(2)
print(coalesceRhs) // 2
```

`??` 연산자는 오른쪽에 위치한 다른 옵셔널 값과도 작동하기 때문에, 여러 개의 `??` 연산자를 함께 사용할 수도 있다.

```swift
let coalesceFirst: Optional<Int> = nil
let coalesceSecond: Optional<Int> = nil

let coalesceChain = coalesceFirst ?? coalesceSecond ?? Int(3)
// coalesceFirst값 체크->nil이므로 coalesceSecond값 체크-> 다시 nil이므로 Int(3)에 도달

print(coalesceChain) // 3
```

---

### Unconditional Unwrap
*일명 강제 언래핑*

단순히 `Optional` 값의 뒤에 `!`를 붙임으로써 사용할 수 있다.
```swift
let soSimple: Optional<Int> = 1
print(soSimple!) // 1
```

하지만 만약 `Optional`이 값을 가지고 있지 않을 경우, 즉시 런타임 오류가 발생한다.
안전성을 위한 `Optional`을 간단하게 풀어버리는 만큼, 안전성을 포기하는 언래핑 방법이라고 할 수 있다.

```swift
let areYouSure: Optional<Int> = nil
print(areYouSure!) // 크래시
```
다만 로직상 `Optional`이 반드시 값을 가지고 있을 수밖에 없는 등 확신이 있다면 편리하게 사용할 수도 있다.

### Implicitly Unwrapped Optional(IUO)
*암시적 추출 옵셔널*

타입의 선언시 타입 뒤에 `!`를 붙임으로써 사용할 수 있다.

IUO로 지정된 타입은 일반 값처럼 사용할 수 있으나, nil을 할당할 수도 있다. 하지만 nil이 할당되어 있을 때 접근하면 런타임 오류가 발생한다.

```swift
var bornToWrap: String! = "good"
print(bornToWrap) // good

bornToWrap = nil
print(bornToWrap) // 크래시
```