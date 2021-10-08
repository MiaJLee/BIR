# 6. 편집기를 사용하여 타입 시스템 탐색하기
편집기에서 타입스크립트 언어 서비스를 활용하자!


# 7. 타입이 값들의 집합이라고 생각하기
## 타입: 할당 가능한 값들의 집합
- `never`: 가장 작은 집합. 공집합으로 어떠한 값도 할당할 수 없다.
- `unit`: 한 가지 값만 포함하는 타입. ex) `type A = 'A';`
- `union`: 두 개 혹은 세 개로 묶은 타입. ex) `type AB = 'a' | 'b';`
- `unknown`: 전체(universal) 집합.
  
## 타입 연산자는 인터페이스의 속성이 아닌, 값의 집합에 적용된다.
```ts
interface Person {
  name: string;
}
interface Lifespan {
  birth: Date;
  death?: Date;
}
type PersonSpan = Person & Lifespan;
```
여기서 `&` 연산자는 값의 집합(타입의 범위)에 적용되기 때문에, 추가적인 속성을 가지는 값도 여전히 타입에 속하게 된다. 따라서 `Person`과 `Lifespan`을 둘 다 가지는 값은 인터섹션 타입에 속한다.
```ts
const ps: PersonSpan = {
  name: 'Alan Turing',
  birth: new Date('1912/06/23'),
  death: new Date('1954/06/07'),
} // 정상, 더 많은 값을 가져도 정상이다.
```
## `extends`의 의미는 `~의 부분 집합`
```ts
interface Person {
  name: string;
}
interface PersonSpan {
  birth: Date;
  death?: Date;
}
```
`PersonSpan`은 `Person`의 서브타입이다. 클래스의 관점에서는 서브 클래스로 볼 수 있다.

`extends`는 제너릭 타입에서 한정자로도 사용된다. 아래의 `K`는 `string`을 상속받는다기 보다는, 부분 집합의 범위를 갖는 타입이 된다고 보는 것이 이해가 쉽다. `string` 리터럴 타입, `string` 리터럴 타입의 유니온, `string` 자신을 포함한다.
```ts
function getKey<K extends string>(val: any, key: K) { ... }
```
## 타입스크립트에서 불가능한 값
정수에 대한 타입, 또는 x와 y 속성 외에 다른 속성이 없는 객체는 타입스크립트 타입에 존재하지 않는다. `Exclude`를 사용해서 일부 타입을 제외할 수는 있으나, 적절한 타입일때만 유효하다.
```ts
type T = Exclude<string|Date, string|number>; // 타입은 Date
type NonZeroNums = Exclude<number, 0>; // 타입은 number
```

# 8. 타입 공간과 값 공간의 심벌 구분하기
- 타입스크립트의 심벌(symbol)은 타입 공간이나 값 공간 중의 한곳에 존재한다. 따라서 이름이 같더라도 속하는 공간에 따라 다른 것을 나타낼 수 있다. 이를 잘 구분해야 한다.
- 따라서 같은 이름의 값으로 `instanceof`를 이용해 타입을 체크하는 등의 연산을 하는 경우, `instanceof`는 자바스크립트의 런타임 연산자이기 때문에 타입이 아닌 함수를 참조하는 이슈가 있을 수 있다.
- 타입 선언(`:`) 또는 단언문(`as`) 다음에 나오는 심벌은 타입인 반면, `=` 다음에 오는 모든 것은 값이다.
- `class`, `enum` 키워드는 값과 타입 두 가지로 모두 사용되기 때문에 주의한다.
- `typeof`, `this` 등의 연산자들과 키워드들은 타입 공간과 값 공간에서 다른 목적으로 사용될 때 상황에 따라 다르게 동작한다.
- 속성 접근자인 `[]`는 타입으로 쓰일 때도 동일하게 동작하지만, `obj['field']`와 `obj.field`는 값이 동일하더라도 타입은 다를 수 있기 때문에 반드시 `obj['field']`를 사용한다.