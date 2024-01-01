---
title: 반복문Iteration
date: 2023-12-29 02:29:46 +0900
categories: [DEVELOPMENT, SWIFT]
tags: [swift, 문법]     # TAG names should always be lowercase
---

> 배열과 같이 순서가 있는 데이터는 반복문으로 쉽게 처리할 수 있다.

반복문이란, 제어문 중 하나로, 소스 코드 내의 특정 코드가 반복적으로 수행될 수 있도록 하는 구문이다.
반복문을 사용하면 조건에 따라 실행되어야 하는 명령어를 조건문을 통해 분기할 수 있다.
대부분의 프로그래밍 언어에는 필수적으로 존재하는 문법으로, for-in문과 do-while(Swift는 repeat-while)문이 있다.

---


# for-in문
>`for-in`구문은 반복적인 데이터나 시퀀스를 다룰 때 유용하다.



```swift
for 루프상수 in 순회대상 {
	코드
}
```
순회 대상은 범위일 수도, 
```swift
for number in 0...10 {
	print(\(number))
}
```
컬렉션일 수도 있다.

```swift
let companies: [String] = ["Apple", "Samesung", "Sony"]

for company in companies {
	print("This \(company)")
```





# while문
> `Bool`타입으로 지정된 특정 조건이 성립하는 한 내부의 코드를 반복해서 실행한다.

```swift
let companies: [Stirng} = ["Apple", "Samesung", "Sony"]
var number: Int = 0
while number < 3
```




# repeat-while문
>```repeat```중괄호 내부의 코드는 조건식에 맞지 않더라도 한번은 무조건 실행한다.

```swift
repeat {
	코드
} while 조건식
```

