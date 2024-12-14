## 타입스크립트 Study

타입스크립트에 공부하고 내용을 정리합니다.

### 타입 엘리어스와 인터페이스 상속

```typescript
type Animal = { breath: true };
type Poyouryu = Animal & { breed: true };
type Human = Poyouryu & { think: true };

interface A {
  breath: true;
}

interface B extends A {
  breed: true;
}

const b: B = { breath: true, breed: true };
const me: Human = { breath: true, breed: true, think: true };
```

- `type` 과 `interface` 에 대한 상속
- 보통 인터페이스를 쓰면 좋음. (확장성 고려)

```typescript
interface A {
  talk: () => void;
}

interface A {
  eat: () => void;
}

interface A {
  shit: () => void;
}

const a: A = { talk() {}, eat() {}, shit() {} };
```

- `interface`는 선언이 여러번 될수 있음
- `interface`는 합쳐질 수 있음
- 다른사람의 인터페이스를 합칠수 있어서 남의 라이브러리를 확장해서 사용가능

### 타입을 집합으로 생각해보기

```typescript
type A = string | number; // 넓은타입
type B = string; // 좁은 타입
type C = string & number; // never (공집합)
```

- 타입을 집합으로 생각하면 `|` 를 사용하면 넓은 범위의 타입이 됨
- `&` 를 사용하면 점점 좁아짐
- `any`: 전체집합, `never`: 공집합 이라고 비유할수 있음

```typescript
type A = { name: string }; // 넓은 타입
type B = { age: number }; // 넓은 타입

type AB = A | B;
type C = A & B; // { name: string, age: number }  // 좁은타입

const ab: AB = { name: "jungwoo" };
const c: C = { name: "jungwoo", age: 29 };

const testC: C = ab; // X
const testAB: AB = c; // O
```

- 객체는 상세할수록, 구체적일수록 좁다.

### 잉여 속성검사

```typescript
interface A = { a: string }
const obj: A = { a: 'hello', b: 'world' } // X
----------------------------------------------
interface A = { a: string }
const obj = { a: 'hello', b: 'world' }
const obj1: A = obj // O

```

- 객체 리터럴일때 넓은타입에 좁은타입을 대입하는데도 에러가 남.
- 다른 객체로 빼서 정의한뒤 대입하면 에러가 나지 않음

### void 의 여러 역할

```typescript
function a(): void {
  return "3"; // X
}
```

- 많은 사람들이 이해한것 처럼 보통 `void` 는 함수에서 리턴값이 없을때 사용됨
- `void` 일 경우 return이 `undefined` 이거나 리턴값이 없을때 사용 (`null` 은 안됨)

```javascript
function a(callback: () => void)
a(() => { return '3'})  // O

interface Human {
  talk: () => void;
}
const human: Human = {
  talk() { return: 'abc'; } // O
}

```

- 매개변수와 메서드는 `void`를 선언하더라도 return 값이 있어도 문제가 없음
- 리턴값을 사용하지 않는다는 의미

```typescript
declare function forEach(arr: number[], callback: (el: number) => void): void;
let target: number[] = [];
forEach([1, 2, 3], (el) => {
  target.push(el);
}); // callback 리턴이 void일때 O, number일때 X
forEach([1, 2, 3], (el) => target.push(el)); // callback 리턴이 void일때 O, number일때 O
```

- `void`를 선언하면 콜백함수의 리턴값이 어떤 타입이든간에 허용하겠다는 의미
- `undefined`와 `void`는 다름
- `declare function` 으로 body를 꼭 명시하지 않아도 되는 함수 타입 설정 가능, 바로 아래에 body 구현하면 declare 필요 없음

```typescript
interface A {
  talk: () => void
}

const a: A = {
  talk:() { return 3 } // O
}

const b = a.talk() // O => b가 void타입이 되어버림
const b = a.talk() as unknown as number;
const b = <number><unkonwn>a.talk()
```

- `void` 타입일경우 반환도 `void`타입이 되어버림
- `as` 또는 제네릭(꺽쇠타입)을 통해 강제로 형변환 해줘야 함
