# iOS 커리어 스타터 캠프
# 계산기
> 숫자와 기호를 이용하여 사칙연산을 하는 계산기 앱 만들기
> 
> 프로젝트 기간: 2023.02.06-2023.02.10
> 
> 팀원: 👩🏻‍💻[리지](https://github.com/yijiye?tab=repositories) 🧑🏻‍💻[vetto](https://github.com/gzzjk159) 🧑🏻‍💻[Andrew](https://github.com/Andrew-0411) | 리뷰어: 👩🏻‍💻[Judy](https://github.com/Judy-999)
>


## 목차
1. [타임라인](#타임라인)
2. [프로젝트 구조](#프로젝트-구조)
3. [실행화면](#실행화면) 
4. [트러블 슈팅](#트러블-슈팅) 
5. [팀 회고](#팀-회고)
6. [참고 링크](#참고-링크)


# 타임라인 
- 2023.02.06 : Model 파일별로 코드 병합, ViewController 메서드별로 코드 병합
- 2023.02.07 : 새로운 브랜치에 PR, Merge 방법으로 코드 병합
- 2023.02.08 : 오류 수정, 코드 리팩토링
- 2023.02.09 : ViewController에서 View분리 및 개행, 주석, 네이밍 리팩토링
- 2023.02.10 : 개행정리, 네이밍 수정, 파일정리 
<br>


# 프로젝트 구조

<details>
    <summary><big>UML</big></big></summary>

![calculator2 drawio](https://user-images.githubusercontent.com/72865221/218009246-7d9579f0-9bb5-4cef-926e-398e36bc1ae8.png)

</details>

<br>
<details>
    <summary><big>코드병합 정리</big></big></summary>

<br/>

**Model**
> 파일별로 병합
- CalculatorItemQueue (vetto)
    - LinkedList 구현 : 팀원이 사용해보지 않은 데이터 구조 선택
- Operator.divide (리지)
    - 오류 처리 구문: 리지가 구현한 NaN 오류처리 선택
- ExpressionParser (Andrew & vetto)
    - `parse` (Andrew)
    - `componentsByOperators` 고차함수 flatMap 이용 (vetto)
- Formula (Andrew & 리지)
    - 프로퍼티, 메서드 (Andrew), do-catch 구문 (리지)
- Extension String (Andrew)
    - 고차함수 활용

<br/>

**ViewController**
> 버튼의 기능에 따라 병합

- AC버튼 (리지) 
    - 메서드로 분리하여 여러곳에서 재활용
- CE버튼 (vetto, 리지) 
    - 둘의 코드가 일치
- +/- 버튼 (vetto)
    - 기능적으로는 유사했으나 사용한 명령어에 따라 직관적인 vetto의 코드를 선택 (vetto: `first`, `removeFirst()`, 리지: `firstIndex(of:)`, `remove(at:)` 사용)
- 결과버튼 (vetto, 리지)
    - 계산하는 과정을 따로 메서드로 분리 (리지)
    - 조건과 NumberFormatter 적용 (vetto)
- 숫자입력버튼 (vetto, 리지)
    - 계산 부울 변수를 초기화 하는 메서드 (리지)
    - 조건과 조건과 NumberFormatter 적용 (vetto)
- 연산자입력버튼 (vetto, 리지)
    - 전반적인 코드 (vetto)
    - `hasSuffix` 로 `.` 으로끝나는 숫자 조건 적용 (리지)
- NumberFormatter적용 (vetto)
    - extension 으로 구현하여 전역에서 활용 (콤마, 불필요한 소수점 제거, 20자리)
- 스택뷰 (리지)
    - UILabel을 stackView의 쌓는 형식
- 스크롤뷰 (vetto)
    - 자동으로 스크롤뷰가 올라오도록 구현할때, `layoutIfNeeded()`사용

**View(Storyboard)**
- 스택뷰를 리지로 선택하면서 스토리보드상 불필요한 스택뷰 제거


</details>

   
# 실행화면
|AC: 모든 연산을 초기화|CE: 현재 입력한 숫자만 삭제|⁺⁄₋: 숫자 부호 변환하여 연산 |
|:---:|:---:|:---:|
|<img src="https://i.imgur.com/CwB6FmZ.gif" style="zoom:33%;"/>|<img src = "https://i.imgur.com/0IAciea.gif" style="zoom:33%;"/>|<img src="https://i.imgur.com/zlbXqU4.gif" style="zoom:33%;"/>|

|숫자입력이 없는 상태인 0에서 연산자를 누르면 연산이 이루어지지 않고 연산자만 변경|숫자 3자리마다 콤마(,)를 표기해서 숫자를 나누고 소수점 뒤에 불필요한 숫자 0은 생략한다|= 버튼을 누르면 연산을 한 번에 수행한다. 연산자 우선순위는 무시되고 순차적으로 연산한다.|
|:---:|:---:|:---:|
|<img src="https://i.imgur.com/7sxRncr.gif" style="width: 300;"/>|<img src = "https://i.imgur.com/IK5eUqs.gif" style="width: 300;"/>|<img src="https://i.imgur.com/WGRjgYt.gif" style="width: 300;"/>|

|0으로 나누기를 시도할때 NaN으로 표기|ScrollView|
|:---:|:---:|
|<img src="https://i.imgur.com/0kveh1b.gif" width= "230" height="430">|<img src="https://i.imgur.com/lUj7m4d.gif" width="230" height="430">|



# 트러블 슈팅 

## 1️⃣ 배열 vs 연결리스트
### 문제점
* 3명의 코드를 합치는데 있어서 queue를 구성하는 방법이 달랐습니다. 한명은 노드를 이용한 LinkedList로 queue를 구성하였고 다른 두명은 기존 Array를 이용한 doubleStack으로 queue를 구현하였습니다. 따라서 어떤 방식을 채택해서 병합할지에 대해 고민을 하였습니다.

### 해결법
* 팀원과 상의한 결과 자신이 사용해보지 않은 방법으로 사용해보고 싶다고 하여 Node를 이용한 LinkedList를 사용한 queue 구성 방식을 채택하여 병합하기로 하였습니다.

```swift
//
final class Node<Element: CalculateItem> {
    let data: Element?
    var next: Node<Element>?
    
    init(data: Element?, next: Node<Element>? = nil) {}
}

struct LinkedList<Element: CalculateItem> {
    private(set) var head: Node<Element>?
    private(set) var tail: Node<Element>?
    
    var isEmpty: Bool {}
    
    mutating func append(_ data: Element) {}
    
    mutating func removeFirst() -> Element? {}
}
```

</br>

## 2️⃣ 전역 변수 사용
- 프로젝트에서 전역변수 expression을 사용해서 연산자와 피연산자를 받고 있습니다. 여러 함수에서 사용하기 때문에 전역으로 사용해도 괜찮은 방법인지 아니면 전역 변수를 지양하는 쪽으로 코드를 짜야하는지 고민했습니다.

### 해결법
- 코드를 합칠 때, 리지는 지역변수를 사용하여 stackView의 subView를 `forEach`로 돌면서 그 값들을 받아 계산을 하였고, vetto는 전역변수를 사용 연산자 버튼이랑 결과 버튼을 눌렀을 때 연산자와 숫자의 Label이 stackView의 들어갈 수 있게 하였습니다. 두가지를 비교했을 때, forEach로 모든 stackView를 도는 것이 메모리를 낭비한다고 판단하여 최소한의 전역변수를 사용한 vetto의 방법을 선택하였습니다.

<br/>

## 3️⃣ 실행 중 오류 해결
### 1. 숫자 뒤에 소수점이 붙은 숫자
### 문제점 
- 숫자 뒤에 소수점을 붙이고 아무 소수점숫자를 적지 않는 상태(ex: 15.)에서 사칙연산을 할 경우 스크롤뷰 연산에 올라가야하는지 고민이였습니다.

### 해결법
- `15.`를 입력하고 연산자를 입력했을 때 `.`은 사라지고 `15`만 스택뷰에 올라가도록 수정하였습니다.

```swift
static func convertToString(fromString: String) -> String {
    let number = fromString.filter({ $0 != Character(Symbol.comma) })
    guard let doubleInput = Double(number) else { return Symbol.empty }
        
    return convertToString(fromDouble: doubleInput)
}
```
위에 코드에서 `"15."`이라는 문자열이 Double()함수에 들어가면 15.0으로 수정되어 나오게 하였습니다.

### 2. 숫자 20자리까지 표현
- Double 타입으로 반환되는 조건에서 20자리까지 표현하는 것이 한계가 있었습니다. 저희는 숫자의 count로 접근하여 20과 같거나 작은 조건을 추가하여 자릿수를 맞추었습니다.

### 3. 불필요한 String 사용 줄이기
### 문제점
- label의 text를 설정해줘야 할 때 연산자가 `""`이 되면 기존 코드에서는 blank(`" "`)를 붙여주는 불필요한 과정이 있었습니다. 

**기존코드**
```swift
label.text = calculateOperator + Symbol.blank + NumberFormatter.convertToString(fromString: calculateOperand)
```

### 해결법
- 아래와 같이 기존의 코드를 if문으로 label의 text를 연산자를 기준으로 비어있을 때와 있을때를 구분하여 text를 지정해주어 메모리의 사용을 줄여주었습니다.

**변경코드**
```swift
private func addStackView() {
    let text: String
    if calculateOperator.isEmpty {
        text = NumberFormatter.convertToString(fromString: calculateOperand)
    } else {
        text = calculateOperator + Symbol.blank +
          NumberFormatter.convertToString(fromString: calculateOperand)
    }
        calculateStackView.addArrangedSubview(ViewManager.generateUILabel(text))
}
```

- 조건문 없이 `text.lengthOfByte`를 확인했을 때
<img src="https://i.imgur.com/TKPNOoC.png" width="500">

</br>

- 조건문을 추가하여 불필요한 String을 제거한 후 `text.lengthOfByte`를 확인했을 때
<img src="https://i.imgur.com/XxBYq1L.png" width="500">

</br>

## 4️⃣ View와 ViewController 분리
### 문제점
- `ViewController` 안에 `UILabel`을 만들어 `StackView`에 쌓이도록 처음에 구현을 했는데, `ViewController`의 코드 길이가 너무 길고 기능을 분리하는게 좋을 것 같다고 생각하였습니다. 그런데 현재 코드에서 View는 스토리보드로 구현을 하였기 때문에 분리하는 것이 큰 의미는 없다고 생각했으나 시험삼아 ViewController에 없어도 되는 Label, stackView, scrollView 일부를 분리시켜 보았습니다.

### 해결법
- `ViewManager` 라는 파일을 따로 만들어 enum으로 구현하고 static을 사용하였습니다. 그 이유는 인스턴스를 만드는 과정이 필요없고, static으로 구현하면서 전역에서 공용으로 접근할 수 있기 때문입니다.

## 5️⃣ ViewManager 파일 위치
### 문제점
- ViewManager는 ViewController의 기능을 일부 분리하여 생성한 파일이므로 ViewController에 넣어두었습니다. 그러나 ViewController이려면 담당하는 View가 있어야 한다는 생각에 기존의 파일 위치를 변경해야 했습니다.

### 해결법
- ViewManager의 역할은 View를 구성한다기 보다 View를 생성해주는 기능을 하는 것이므로 MVC 셋 중에 하나로 구분하기 보다 기능을 담당해주는 Utility로 구분하였습니다.

## 6️⃣ swift style convention
### 문제점
- swift에서는 기존의 코드가 한줄에 100자를 넘지 않는 것을 선호하고 100자가 넘는 코드에 대한 개행을 어떻게 나누면 좋을지 고민하였습니다.

### 해결법
- 컨벤션은 결국 협업자간의 규칙이므로 3명의 의견이 동의하는 방법을 선택하는 것이 가장 적절하다고 생각하였습니다. 팀원들과 상의하여 아래와 같이 개행정리를 하였습니다.

```swift
else {
    calculateOperand = NumberFormatter
        .convertToString(fromString: calculateOperand + number
    )
}
```
```swift
else {
    text = calculateOperator
    + Symbol.blank
    + NumberFormatter.convertToString(fromString: calculateOperand)
}
```

# 팀 회고
## 잘한 점
- MVC 디자인패턴을 사용함으로써 로직(Model)과 인터페이스 요소(View)를 분리시켜 각각 독립적으로 사용하고 효과적으로 로직을 재사용 하였습니다.
- 서로의 코드를 설명하고 장단점을 파악하여 적절한 코드 병합을 하였습니다.
- 팀원들이 사용해보지 않은 자료구조(Queue, Linkedlist)를 사용함으로써 팀원들의 자료구조의 이해를 높였습니다.
- 다양한 방면으로 코드를 고민하고 여러 시도를 하였습니다.
    
## 보완하고 싶은 점
- MVC 디자인패턴은 View와 Model 사이의 의존성이 높아지고 Controller가 비대해진다는 단점이 있는데 이것을 보완할 수 있는 MVVM 디자인패턴을 사용하면 더 의존성을 낮출 수 있으면 좋겠다고 생각했습니다. 
- MVC 패턴, UML class Diagram에 대해 명확하지 않은 부분이 있어 부족한 부분에 대한 학습이 필요하다 생각했습니다.

## 팀원 서로 칭찬하기

### vetto, 리지 -> Andrew
- 같이 고민하는 부분에 대해 자료를 찾아봐주고, 문제가 있는 부분도 적극적으로 해결하려고 한 점이 좋았습니다 👍
- 자신의 의견을 충분히 어필하여, 팀원들이 더 다양한 생각을 할 수 있게 해주었습니다

### 리지, Andrew -> vetto
- 실행이 잘 되기 때문에 모르고 넘어갈 수도 있는 기능의 중복을 예리하게 찾아내어서 refactor 하는것에서 많은것을 배웠습니다.
- 코드에 대해 잘 설명해주고 어려운 부분이 있을 때, 다른 캠퍼들에게 솔선수범으로 질문해서 문제를 해결하는데 적극적으로 한 부분이 좋았습니다 👍

### Andrew, vetto -> 리지
- 팀원들이 생각하지 못한 부분을 생각하여 코드가 비대해지는 것을 줄여줬습니다.
- 현재의 코드에 만족하기보다는 좀 더 적합한 코드를 위해 자료를 찾고 적극적으로 변경을 시도하여서 코드를 더 깔끔하게 다듬어 주었습니다.

---- 

# 참고 링크
- [Swift Language Guide - Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
- [Swift Language Guide - Extentions](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)
- [Swift Language Guide - Error Handling](https://docs.swift.org/swift-book/LanguageGuide/ErrorHandling.html)
- [Swift Language Guide - Closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)
- [Swift Language Guide - Advanced Operators](https://docs.swift.org/swift-book/LanguageGuide/AdvancedOperators.html)
- [Swift Language Guide - Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html)
- [Swift Language Guide - Subscripts](https://docs.swift.org/swift-book/LanguageGuide/Subscripts.html)
