### 06
**ide를 이용한 타입 추론**
- 자바스크립트에서 최상위 객체는 object
- - typeof null = "object"

### 07
**타입이 값들의 집합이라고 생각하기**
- 집합중 가장 작은 집합 (아무것도 포함이 되지 않는)은 공집합
타입스크립트에서는 never타입 

- 그 다음으로 작은 집합은 한 가지 값만 포함하는 타입
타입스크립트에서는 **유닛(unit)타입이라고도 불리는 리터럴(literal)**

- 유니온 타입   `|` 로 묶어주면 됨
``` typescript
type A = 'A';
type B = 'B';
type T = 12 ;
type AB = 'A' | 'B' ;
const a : AB = 'A';
const c : AB = 'C'; //error c는 유닛타입
```

- 인터페이스 이용하기
