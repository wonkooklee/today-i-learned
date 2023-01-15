# Proxy와 Reflect에 대한 Toast UI 포스트 필기

- https://ui.toast.com/posts/ko_20210413#12-메타-언어와-대상-언어가-같은-경우

<br />
<br />

# 메타프로그래밍

## 메타 언어와 대상 언어가 다른 경우

- 메타프로그래밍이란 하나의 컴퓨터 프로그램이 다른 프로그램을 데이터로 취급하는 것을 말한다.
- 메타프로그래밍에 이용되는 언어를 메타 언어, 조작 대상이 되는 언어를 대상 언어라고 한다.
- 예를 들어 JSP에서 HTML을 만들어내는 코드에선 Java가 메타 언어, HTML이 대상(언어는 아니기에)이 될 것이다.

## 메타 언어와 대상 언어가 같은 경우

- 한 프로그래밍 언어가 자기 자신의 메타 언어가 되는 것을 반영(Reflection)이라고 한다.
- 런타임 시점에 자신의 구조와 행위를 관리하고 수정하는 것을 의미한다.

```js
eval("1 + 1");
// 2
```

## 반사형 프로그래밍(Reflective programming)

- Type introspection
  런타임에서 프로그램이 자신의 구조에 접근하여 타입이나 속성을 알아내는 것. (e.g. Object.keys())
- Self-modification
  구조를 스스로 변경할 수 있다는 의미. 속성 접근자 [], delete 연산자로 제거하는 것 등.
- Intercession
  어떤 것을 대신(proxy)하여 개입하는 행위를 뜻하며, 언어가 수행되는 일부 의미를 재정의하는 것.
  이를 지원하기 위해 ES2015에서 Proxy를 만듬.

# Proxy란

- 프록시 객체(Proxy Object)는 대상 객체(Target Object) 대신 사용된다.
- 프록시 객체가 사용되며 각 작업을 대상 객체로 전달하고 결과를 다시 코드로 돌려준다.
- 이런 방식을 통해 프록시 객체는 JavaScript의 기본적인 명령에 대한 동작을 사용자 정의가 가능하도록 한다.
- 객체 자체가 처리하는 특정 명령을 재정의하는 것(속성 검색, 접근, 할당, 열거, 함수 호출 등)

```js
// Proxy 객체 생성
const p = new Proxy(
  {
    /* target */
    /* Intercession 처리를 해야 하는 대상 객체 */
  },
  {
    /* handler */
    /* Intercession에 사용될 핸들러(트랩)를 추가할 때 사용되는 객체 */
  }
);
```

변수 p 내부는 아래와 같음.

```
> p;
Proxy {}
  > [[Handler]]: Object
  > [[Target]]: Object
  > [[IsRevoked]]: false
```

`[[]]`와 같은 슬롯은 JavaScript 내부 슬롯(Internal Slot)이라 하며 코드로 접근 불가함.

- `[[Handler]]` : 두 번째 인자로 전달할 객체를 맵핑
- `[[Target]]` : Proxy 처리가 될 대상을 뜻하며 첫번째 인자로 전달한 객체
- `[[IsRevoked]]` : 폐기 여부를 뜻함

- 한 번 세팅된 프록시 객체의 대상 객체는 변경될 수 없다.

### 트랩

프록시 객체는 대상 객체의 기본 명령을 재정의하기 위한 함수인 트랩(Trap)을 통해 동작하는 매커니즘을 가진다.

```js
const target = { name: "target" };
const t = new Proxy(target, {
  get: () => console.log("access"),
});
t.name; // access
```

### 폐기 가능한 프록시 객체

생성자로 만들어진 프록시 객체는 지워지거나 재사용이 불가하다.
필요에 따라 폐기 가능한 프록시 객체(revocable proxy)를 만들 수도 있다.

```js
const revocable = Proxy.revocable({}, {});
```

revocable() 메서드는 객체와 revoke() 메서드가 담겨있는 객체를 반환함.

```
> revocable;
{proxy: Proxy, revoke: f}
> revocable.proxy;
Proxy {}
  > [[Handler]]: Object
  > [[Target]]: Object
  > [[IsRevoked]]: false
```

# Reflect

Reflect는 Proxy와 같이 JavaScript 명령을 가로챌 수 있는 메서드를 제공하는 내장 객체이다.

## Reflect 특징

- 함수 객체가 아닌 일반 객체
- [[Prototype]] 내부 슬롯을 가지며 그 값은 Object.prototype (Reflect.**proto** === Object.prototype)
- [[Construct]] 내부 슬롯이 존재하지 않아 new 연산자를 통해 호출될 수 없음.
- [[Call]] 내부 슬롯이 존재하지 않아 함수로 호출될 수 없음.
- Proxy의 모든 트랩을 Reflect의 내장 메서드가 동일한 인터페이스로 지원함.

```js
// 1. 에러 핸들링
const obj = {};
try {
  Object.defineProperty(obj, "prop", { value: 1 });
  console.log("success");
} catch (error) {
  console.log(error);
}

const obj2 = {};
if (Reflect.defineProperty(obj2, "prop", { value: 1 })) {
  console.log("success");
} else {
  console.log("problem creating prop");
}

// 2. 더 읽기 쉬운 코드
const obj3 = { prop: 1 };
console.log(Reflect.has(obj, "prop") === "prop" in obj);

// 3. 안정적인 호출
const obj4 = { prop: 1, hasOwnProperty: 2 };
// obj4.hasOwnProperty("prop");
// Uncaught TypeError: obj4.hasOwnProperty is not a function
// no-prototype-builtins: 기본 객체 내장 메서드명이 인스턴스 객체의 속성명일 경우 버그를 방어하기 위해 에러 발생.

// 기존 방식
Object.prototype.hasOwnProperty.call(obj4, "hasOwnProperty"); // true
// 새로운 문법
Reflect.has(obj4, "hasOwnProperty"); // true
```

## Reflect.get과 Reflect.set

```
Reflect.get(target, propertyKey [, receiver])
```

- target이 객체가 아닐 경우 TypeError 발생(기존 접근자는 에러 발생 대신 undefined로 평가)

```
Reflect.set(target, propertyKey, V [, receiver])
```

## receiver

ECMAScript의 속성 탐색과정
Bracket notation or Dot notation

`obj.prop = V` 또는 `obj[prop] = V`를 사용하면
GetValue(V) 호출하고, 내부 슬롯 메서드인 [[Get]](P, Receiver)를 호출함.

```js
const child = { age: 0 };
const parent = { age: 40, job: "programmer" };

child.__proto__ = parent;
console.log(child.job); // programmer
```

처리 과정:

- GetValue(V) 호출시

  - 탐색하고자 하는 속성명 = job
  - Receiver는 GetThisValue(V)의 결과값으로, this 컨텍스트인 child가 됨
  - [[Get]](P, Receiver)가 호출되면, OrdinaryGet(O, P, Receiver)를 호출한다.
  - O는 child, P는 job, Receiver는 child가 된다.
  - child에 해당 속성이 없으면 프로토타입 체이닝을 통해 parent.[[Get]](P, Receiver)를 재귀적으로 호출한다.
  - OrdinaryGet(O, P, Receiver)로 값을 찾으면 O는 parent가 된다. job을 찾고 'programmer'를 반환하게 된다.

  - 중요한 점은 프로토타입 객체로 탐색을 하더라도 Receiver가 유지된다는 점이다.

```js
const anotherChild = {
  birthYear: 2021,
};

const anotherParent = {
  birthYear: 1990,
  get age() {
    return new Date().getFullYear() - this.birthYear;
  },
  set birthYear(year) {
    this.birthYear = year;
  },
};

anotherChild.__proto__ = anotherParent;
console.log(anotherChild.age); // 2

anotherChild.birthYear = 2017;
console.log(anotherChild.age); // 6
```

위 코드의 흐름은 다음과 같다.

child.[[Get]](P, Receiver)가 호출되고, Receiver는 child가 된다.
프로토타입 체이닝에 의해 parent.[[Get]](P, Receiver)가 재귀적으로 호출되고 age가 getter로 존재하며 이를 실행할 때 Receiver가 this로 사용된다.

Receiver는 작업 요청을 직접 받은 대상을 말한다.
따라서 Getter의 this는 parent가 아닌 child가 된다.

## Reflect.get과 Reflect.set의 receiver

Reflect는 receiver를 통해 this 바인딩을 조절할 수 있다.

```js
const ref1 = {
  a: 1,
  b: 2,
  get sum() {
    return this.a + this.b;
  },
};

const receiverRef1 = { a: 2, b: 3 };

Reflect.get(ref1, "sum", ref1);
// 해석: ref1의 sum 속성에 접근하되 this 레퍼런스를 전달한 ref1로 참조해줘.

console.log(Reflect.get(ref1, "sum", ref1));
// 3

console.log(Reflect.get(ref1, "sum", receiverRef1));
// 바인딩을 조작하여 this는 receiverRef1 객체가 되고 결과는 5

const setProp = {
  prop: 1,
  set setProp(value) {
    return (this.prop = value);
  },
};

const receiverSetProp = {
  prop: 0,
};

console.log(Reflect.set(setProp, "setProp", 2, setProp)); // true
console.log(setProp.prop); // 2
console.log(Reflect.set(setProp, "setProp", 1, receiverSetProp)); // true
console.log(receiverSetProp.prop); // 1
```

결론적으로 JavaScript는 getter/setter일 때 프로토타입 체이닝을 하더라도
최초 속성 접근 요청을 받은 객체를 Receiver에 담아 유지하고 있으며,
Reflect의 get/set 트랩에서는 receiver 매개변수를 통해 이를 컨트롤할 수 있게 된다.

Vue 개발자인 Evan You는 프로토타입에 대한 사이드 이펙트를 처리하기 위해 사용했다고 한다.
프록시 객체인 반응형 객체를 프로토타입으로 사용할 경우

```js
function reactive(target) {
  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      const res = target[key];
      return res;
    },
    set(target, key, value, receiver) {
      const oldValue = target[key];
      target[key] = value;
      if (oldValue !== value) {
        // trigger(target, key, value, oldValue);
      }
      return value;
    },
  });
  return proxy;
}

// 위 코드를 기반으로 reactivityParent를 프로토타입으로 둔 child를 조작하는 예제

const rChild = {
  birthYear: 2019,
  // age 없음, __proto__ 체인을 통해 탐색을 이어감
  // parent의 [[Get]]이 호출되면 Proxy get 트랩이 트리거되고
  // target은 parent이기 떄문에 target[key]를 조회하면
  // parent.age의 평가와 똑같아지므로 this는 parent가 됨.
  // 즉, 기존 프로토타입 체이닝이 receiver를 작업 지시를 받은 원본 객체로 유지하고 있는것과 달리
  // target이 값이 참조되는 객체로 정해지기 때문에 prototype 사이드 이펙트가 발생함.
};

const rParent = {
  birthYear: 1990,
  get age() {
    return new Date().getFullYear() - this.birthYear;
  },
};

const reactivityParent = reactive(parent);
rChild.__proto__ = reactivityParent;
console.log(rChild.age); // 40!!!!

Reflect를 통해 receiver를 이용
function reactiveNoSideEffect(target) {
  const proxy = new Proxy(target, {
    get(target, key, receiver) {
      const res = Reflect.get(target, key, receiver);
      // receiver를 전달받는다. 여기서 receiver는 child가 된다.
      return res;
    },
    set(target, key, value, receiver) {
      const oldValue = target[key];
      const res = Reflect.set(target, key, value, receiver);
      // receiver를 전달받는다. 여기서 receiver는 child가 된다.
      if (oldValue !== res) {
        // triggering hooks
      }
      return res;
    },
  });
  return proxy;
}

const goodChild = {
  birthYear: 2019,
};

const goodParent = {
  birthYear: 1990,
  get age() {
    return new Date().getFullYear() - this.birthYear;
  },
};

const reactiveGoodParent = reactiveNoSideEffect(goodParent);
goodChild.__proto__ = reactiveGoodParent;

console.log(goodChild.age); // 4
console.log(goodParent.age); // 33
console.log(goodChild.hasOwnProperty("job")); // false
console.log((goodChild.job = "unemployed")); // unemployed
console.log(reactiveGoodParent.hasOwnProperty("job")); // false
console.log(reactiveGoodParent.job); // undefined
```

<br />
<br />

이상, Proxy에 Reflect를 곁들인 이유.  
https://ui.toast.com/posts/ko_20210413#12-메타-언어와-대상-언어가-같은-경우
