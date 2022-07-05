# 타입스크립트 아랑보기 

## 주요 아이템들 
- 타입스크립트란 무엇인가
- 타입스크립트를 어떻게 여겨야 되는가
- 자바스크립트와는 어떤 관계인지?
- 타입스크립트의 타입들은 null이 가능한지
- any 타입에서는 어떨지
- 덕 타이핑이 가능한지 

### 아이템 1
**타입스크립트와 자바스크립트 관계**
- **타입스크립트**는 **자바스크립트**의 상위집합
모든 자바스크립트 프로그램은 이미 타입스크립트의 프로그램 
반대로 **타입스크립트**는 별도의 문법을 가지고 있기 때문에 유효한 자바스크립트 프로그램이 아니다.
- 타입스크립트는 자바스크립트 런타임 동작을 모델링하는 타입 시스템을 가지고 있기 때문에 런타임 오류를 발생시키는 코드를 찾으려 한다.
하지만 모든 오류를 찾아내리라 기대하면 안됨 
타입 체커를 통과하면서도 런타임 오류있는 코드는 충분히 존재 할 수 있음
- 타입스크립트 타입 시스템은 전반적으로 자바스크립트 동작을 모델링
그치만 자바스크립트에서는 허용되지만 타입스크립트에서는 문제가 되는 경우가 있음

### 아이템 2 
**타입스크립트 설정 이해하기**
- 타입스크립트 컴파일러는 매우 많은 설정을 가지고 있음 (책 기준 약 100개)
- 설정파일은 `명령어`  `파일(tsc --init)`을 이용한 방법이 있음
``` typescript
//noImplictAny

//strictNullChecks
const x:  number = null;  //에러
//명시적으로 null을 허용할려면
const x: number | null = null;
//null을 허용하지 않을려면 null check assertion이 필요함
const el = document.getElementById('status');
el.textContent = 'Ready';
if (el) {
    ...
}
...
```

### 아이템3 
**코드 생성과 티입이 관계없음을 이해하기**
- 타입스크립트 컴파일러는 두 가지 역할을 수행
- - 최신 타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트래스파일(transfile)함
- - 코드의 타입 오류를 체크
- 타입 오류가 있는 코드도 컴파일 가능 
- - 컴파일은 타입체크와 독립적으로 동작 하기 때문에
- 런타임에는 타입 체크가 불가능
- - 타입스크립트의 타입은 제거 가능(`erasable`)
실제로 자바스크립트로 컴파일되는 과정에서 모든 인터페이스,타입,타입 구문은 그냥 제거되어 버림

**그렇다면 어떻게 타입을 명확하게 할까?
(런타임에 타입 정보를 유지)**
- 속성 체크
- 태그 기법
```typescript
interface Square {
    kind : 'square';
    width : number;
}
interface Rectangle {
    kind : 'rectangle';
    height : number;
    width : number;
}
type Shape = Square | Rectangle
function calculateArea(shape : Shape) {
    if(shape.kind == 'rectangle'){
        shape; // type Rectangle
        return ...
    }else{
        shape ; // type Square
        return ...
    }
}
```
- 타입(런타임 접근 불가)과 값(런타임 접근 가능)을 둘 다 사용하는 기법
``` typescript
class Square{
    constructor (public width : number) {}
}
class Rectangle extends Square{
    constructor(public width : number , public height : number){
        super(width);
    }
}
type Shape = Square | Rectangle;

function calculateArea(shape : Shape){
    if (shape instanceof Rectangle){
        shape ; // type Rectangle
        return ...
    }else{
        shape ; //type Square
        return ...
    }
}

```
**구분 할 것 (타입으로 참조 값으로 참조)**

- 타입 연산은 런타임에 영향을 주지 않음
``` typescript
function asNumber(val : number | string) : number {
    return val as number;
}
// -> 하지만 변환된 자바스크립트를 보면 코드에 아무런 정제 과정이 없음
// as number는 타입 연산  런타임 동작에는 아무런 영향이 없음
function asNumber(val : number | string) : number {
    return typeof (val) === 'string' ? Number(val) : val;
}
```
- 런타임 타입은 선언된 타입과 다를 수 있음
- 타입스크립트는 타입으로는 함수를 오버로드 할 수 없음
- - 타입스크립트가 함수 오버로딩 기능을 지원하지만 온전히 타입 수준에서만 동작, 여러개의 선언문을 작성할 수 있지만 , 구현체(`implements`)는 오직 하나뿐

#### remind🙏
- 타입스크립트 타입은 런타임에서 사용할 수 없음 
-> 타입 정보를 유지할려면 ❓
**태그된 유니온과 속성 체크 방법**
**클래스**(타입 스크립트 타입 , 런타임 값 둘 다 제공)

### 아이템 5
**any type 지양하기**
- any type은 안정성이 없음
- any는 함수 시그니처를 무시
- -  시그니처란:
함수의 파라미터 타입이나 리턴 타입을 지정
`contract`라고도 불림
```typescript
function calculateAge( birthDate : Date) : number {
    //..
}
let birthDate : any = '1980-01-19'
calculateAge(birthDate) // 정상
// birthDate 매개변수는 date 타입이여야 하는데 any 타입을 사용하게 되면 함수의 시그니처를 무시하게 됨
```
- any 타입은 코드 리팩터링 때 버그를 감춤
- any는 타입 설계를 감춤
- any는 타입시스템의 신뢰도를 떨어트림