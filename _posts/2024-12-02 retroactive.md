---
title: Swift - 버전 6의 새 어트리뷰트 @retroactive
date: 2024-12-02 18:50:00 +0900
categories: [SWIFT, SOFTWARE]
tags: [cs]     # TAG names should always be lowercase
---

오래된 코드를 열어봤는데 못 보던 `warning`이 표시되었다.
![image](/assets/img/retroactive_1.png)
평범하게 `Date`를 확장하는 코드에 뜬 노란 경고
내용을 살펴보자.
![image](/assets/img/retroactive_2.png)
> Extension declares a conformance of imported type 'Date' to imported protocol 'RawRepresentable'; this will not behave correctly if the owners of 'Foundation' introduce this conformance in the future
{: .prompt-warning }

> 확장은 import된 타입 `Date`의 import된 프로토콜 `RawRepresentable`에 대해 준수하도록 선언됐다; 미래에 `Foundation`프레임워크의 소유자가 준수를 추가하면 이 확장은 올바르게 동작하지 않을 수 있다.

미래에 대한 이야기를 하고 있다.

Fix를 누르면,

```Swift
extension Date: @retroactive RawRepresentable
```
단순히 프로토콜 앞에 `@retroactive` 어트리뷰트가 추가되고, 경고는 사라진다.
무슨 일이 일어난 거지?

retroactive라는 다소 생소한 단어에 대해 찾아보니, `소급 적용`이라고 한다.

오늘은 Swift6에서 추가된 `@retroactive` 어트리뷰트에 대해 알아보자.
이하는 `retroactive-conformance-warning`에 대한 제안 문서의 내용을 기반으로 한다.
https://github.com/swiftlang/swift-evolution/blob/main/proposals/0364-retroactive-conformance-warning.md

제안자는 말한다.
_어떤 라이브러리가 핵심 API 중 하나로 `Date`가 컬렉션의 요소를 identity를 기준으로 비교하는 API와 함께 사용하기 위해 `Identifiable`을 준수하도록 선언했다고 가정해보자._
```Swift
extension Date: Identifiable {
	public var id: TimeInterval { timeIntervalSince1970 }
}
```
_클라이언트가 이 준수를 선언함에 따라, `Foundation`이 이후 리비전에서 이 준수를 다시 선언하면, 클라이언트는 빌드에 실패한다._

클라이언트가 이전에 프레임워크의 특정 프로토콜을 준수하도록 타입을 확장했을 때, 만약 미래에 해당 프레임워크의 소유자가 공교롭게도 같은 준수를 다르게 선언한다면 일어나는 충돌에 대해 이야기하고 있다. 컴파일러는 어떤 정의가 옳은 것인지 알 수 없고, 만약에 잘못된 정의를 적용한다면 완전히 틀린 동작을 할 수 있기 때문에 빌드시킬 수 없다.

때문에 이 어트리뷰트가 제안되었다.

_이 제안은 이 패턴이 문제가 있고 지원되지 않는다는 것을 명시적으로 지적하는 경고를 추가한다.
절대적으로 필요한 경우, 클라이언트는 새 어트리뷰트, `@retroactive`를 해당 프로토콜에 추가하여 이 경고를 끌 수 있다._
_컴파일러는 계층에 포함된 각 프로토콜에 대해 명시적인 `@retroactive` 적합성이 있어야 함을 강조한다. 필요하다면, 컴파일러는 계층의 각 retroactive 준수를 위한 확장을 생성하도록 fix-it을 출력한다._

이 프레임워크에서 네이티브로 선언된건지, 아니면 선언되기 이전에 클라이언트에서 임의로 선언한 것인지 구분해준다는 이야기 같다. 최신 리비전에 맞게 재깍재깍 유지보수하지 않은 코드라도, 바로 예상치 못한 동작을 하는 것을 어느정도 유예해주는, 어떻게 보면 하위호환이 담보되는 느낌...? 유연성 측면에서 좋은 변화라고 생각된다.

다음은 ABI Stability에 대해 써봐야겠다.

흠...그러면 이제 웬만한 확장에는, @retroactive가 붙어야만 하는 건가? 어느 정도 제약은 있는 것 같지만... 어트리뷰트가 늘어가는 스위프트...!
