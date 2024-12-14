## 타입스크립트 Study

타입스크립트에 공부하고 내용을 정리합니다.

### 타입 엘리어스와 인터페이스 상속

```
type Animal = { breath: true };
type Poyouryu = Animal & { breed: true };
type Human = Poyouryu &  { think : true };

interface A {
  breath: true
}

interface B extends A {
  breed: true
}

const b: B = { breath: true, breed: true }
const me: Human = {breath: true, breed: true, think: true}
```

- `type` 과 `interface` 에 대한 상속
- 보통 인터페이스를 쓰면 좋음. (확장성 고려)

```
interface A {
  talk: () => void;
}

interface A {
  eat: () => void;
}

interface A {
  shit: () => void;
}

const a: A = { talk() {}, eat() {}, shit() {}}
```

- `interface`는 선언이 여러번 될수 있음
- `interface`는 합쳐질 수 있음
- 다른사람의 인터페이스를 합칠수 있어서 남의 라이브러리를 확장해서 사용가능

### 타입을 집합으로 생각해보기

```
type A = string | number; // 넓은타입
type B = string; // 좁은 타입
type C = string & number // never (공집합)
```

- 타입을 집합으로 생각하면 `|` 를 사용하면 넓은 범위의 타입이 됨
- `&` 를 사용하면 점점 좁아짐
- `any`: 전체집합, `never`: 공집합 이라고 비유할수 있음

```
type A = { name: string } // 넓은 타입
type B = { age: number } // 넓은 타입


type AB = A | B
type C = A & B // { name: string, age: number }  // 좁은타입

const ab: AB = { name: 'jungwoo'};
const c: C = { name: 'jungwoo', age: 29 }

const testC: C = ab // X
const testAB: AB = c // O

```

- 객체는 상세할수록, 구체적일수록 좁다.
