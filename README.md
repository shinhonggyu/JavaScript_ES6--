#### 자바스크립트 엔진❗

- JavaScript 엔진은 JavaScript 코드를 제공하는 컴퓨터 프로그램으로 컴퓨터에 실행 방법을 알려줍니다.
- 엔진 내부에서는 어떤 일이 발생하는지는 엔진에 달려 있습니다

![Image description](https://images.ctfassets.net/aq13lwl6616q/3o7Q3edCrVJG9Zzj6VMZ1K/28136a643636dfa04090f3fb5c5467ff/javascript_engine.png)

---

#### 인터프리터와 컴파일❗

- 인터프리터는 기계 언어 프로그램으로 컴파일 할 필요없이 코드의 각 줄을 한 줄씩 직접 실행합니다.
- 컴파일러는 컴퓨터에서 읽고 실행할 수 있도록 미리 명령을 기계 코드 또는 하위 수준 형식으로 변환합니다.
- JS가 처음나왔을때(스파이더몽키엔진) 인터프리터언어였지만 구현에따라 컴파일러(Babel, TS)를 이용해 코드최적화가 가능하도록 진화함.
- JIT Compiler: 최신 엔진에서 인터프리터는 코드를 한 줄씩 읽기 시작하고 프로파일 러는 자주 사용되는 코드와 플래그를 감시 한 다음 최적화를 위해 컴파일러에 전달합니다.
  결국 JavaScript 엔진은 인터프리터가 출력하는 바이트 코드를 가져와 컴파일러가 출력하는 최적화 된 코드를 혼합 한 다음이를 컴퓨터에 제공

#### Memoization❗ 컴파일러가 최적화하는데 도움이되는 코드 작성하기

```js
// Bad Way
function addTo80(n) {
  console.log('long time...')
  return n + 80
}

addTo80(5)
addTo80(5)
addTo80(5)

// long time... 85
// long time... 85
// long time... 85

// Memoized Way
function memoizedAddTo80() {
  let cache = {}
  return function(n) { // closure to access cache obj (중첩된 함수에서 자식의함수가 부모함수에 정의된 변수들에 접근이가능한 것)
    if (n in cache) {
      return cache[n]
    } else {
      console.log('long time...')
      catch[n] = n + 80
      return catch[n]
    }
  }
}

const memoized = memoizedAddTo80()

console.log('1.', memoized(5))
console.log('2.', memoized(5))
console.log('3.', memoized(5))
console.log('4.', memoized(10))

// long time...
// 1. 85
// 2. 85
// 3. 85
// long time...
// 4. 90
```

---

#### 메모리힙과 콜스택(LIFO)⭐

- 메모리힙(필요에 따라 메모리를 할당, 사용 및 제거하는 장소)

```js
// tell the memory heap to allocate memory for a number
const number = 11;
// allocate memory for a string
const string = 'some text';
// allocate memory for an object and it's values
const person = {
  first: 'Brittney',
  last: 'Postma',
};
```

- 콜스택(LIFO)(실행중인 함수를 한줄한줄씩 추적)

```js
function subtractTwo(num) {
  return num - 2;
}

function calculate() {
  const sumTotal = 4 + 5;
  return subtractTwo(sumTotal);
}

debugger;
calculate();
```

---

#### Garbage Collection

- 함수 내부에 메모리를 할당하면 함수 호출이 완료되면 JavaScript가 자동으로 메모리 힙에서 메모리를 제거합니다.
- 그러나 그렇다고해서 메모리 누수를 잊을 수 있다는 의미는 아닙니다. 완벽한 시스템은 없으므로 항상 메모리 관리를 기억하는 것이 중요합니다.

```js
let person = {
  first: 'Brittney',
  last: 'Postma',
};

person = 'Brittney Postma';
```

#### 메모리누수

위의 예에서는 메모리 누수가 발생합니다. 변수 person을 객체에서 문자열로 변경하면 first와 last의 값을 메모리 힙에 남겨두고 제거하지 않습니다  
변수를 전역 네임 스페이스에서 제외하고 가능한 경우 함수 내부에서만 변수를 인스턴스화하여이를 방지 할 수 있습니다.

---

#### 이벤트루프 ⭐

- 프로세스(운영체제위에서 독립적으로 메모리에서 실행되고있는 프로그램, 코드-스택-힙-데이터로 구성)
- 쓰레드(프로그램 안에서 동시에 여러개가수행될수있는 작은 일꾼단위,각각 고유스택을 가지고있고 프로세스의 코드-힙-데이터에 공통적으로 접근)
  > JavaScript는 싱글스레드 언어이므로 한 번에 한 가지만 실행할 수 있습니다. 콜스택이 하나뿐이므로 동기 언어이지만 JS가 동작하고있는 브라우저의 멀티쓰레딩인 WebAPIs 이용해 멀티쓰레딩이 가능하다

```js
example;
console.log('1');
setTimeout(() => {
  console.log('2');
}, 0);
// Web APIs 로 전송!
// Web APIs가 1 초를 기다린 후 실행되고 콜백큐로 전송.
//자바 스크립트 엔진은 계속 작동.
console.log('3');
// 이벤트 루프가 계속 확인하고 호출 스택이 비어 있음을 확인.
// 이벤트 루프는 콜백큐를 콜스택으로 보냄.
```

1. 이벤트루프는 콜스택에서 수행중인함수가있다면 끝날떄까지 머무른다
2. 60frames에 맞추어 주기적으로 Render를 업데이트해준다
3. MicroTaskQueue에 콜백함수가있다면 MicroTaskQueue가 빌떄까지 콜백함수를 콜스택으로 가지고와 수행함
4. TaskQueue(Callback Queue)에서는 한번에 하나의콜백함수만 가지고온다(콜백큐는 호출 스택이 완전히 비워 질 때까지 실행할 수 없습니다.)
5. 렌더업데이트시간이되면 requestAnimationFrame을 통해 등록된 콜백함수실행 -> RenderTree->layout->paint(레이어단위로 페인트를준비)->composition -> •••

#### JS 런타임⭐ Playground [here](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gcHJpbnRIZWxsbygpIHsNCiAgICBjb25zb2xlLmxvZygnSGVsbG8gZnJvbSBiYXonKTsNCn0NCg0KZnVuY3Rpb24gYmF6KCkgew0KICAgIHNldFRpbWVvdXQocHJpbnRIZWxsbywgMzAwMCk7DQp9DQoNCmZ1bmN0aW9uIGJhcigpIHsNCiAgICBiYXooKTsNCn0NCg0KZnVuY3Rpb24gZm9vKCkgew0KICAgIGJhcigpOw0KfQ0KDQpmb28oKTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

#### MicroTaskQueue❗

1. 마이크로 태스크큐는 ES6에서 Promise와 함께 나옴.
2. promise를 사용하려면 promise 호출에 더 높은 우선 순위를 부여하는 또 다른 콜백큐가 필요했습니다.
3. JavaScript 엔진은 콜백큐 전에 MicroTaskQueue를 확인합니다.

```js
setTimeout(() => {
  console.log('1');
}, 0);
setTimeout(() => {
  console.log('2');
}, 10);
Promise.resolve('hi').then((data) => console.log('2', data));
console.log('3');

// 3
// 2 hi
// 1
// 2
```

---

#### Node.js ⭐

JS를 브라우저밖에서 무언가를 빌드할수있게 실행시켜주는 실행프로그램

---

#### 3 Ways to Promise

There are 3 ways you could want promises to resolve,  
병렬 (all together), 순차적 (1 after another), or 경쟁 (doesn't matter who wins).

```js
// when new Promise is created, the excutor runs automatically.
const promisify = (item, delay) =>
  new Promise((resolve) => setTimeout(() => resolve(item), delay));

const a = () => promisify('a', 100);
const b = () => promisify('b', 5000);
const c = () => promisify('c', 3000);

async function parallel() {
  const promises = [a(), b(), c()];
  const [output1, output2, output3] = await Promise.all(promises);
  return `parallel is done: ${output1} ${output2} ${output3}`;
}

async function sequence() {
  const output1 = await a();
  const output2 = await b();
  const output3 = await c();
  return `sequence is done: ${output1} ${output2} ${output3}`;
}

async function race() {
  const promises = [a(), b(), c()];
  const output1 = await Promise.race(promises);
  return `race is done: ${output1}`;
}

sequence().then(console.log);
parallel().then(console.log);
race().then(console.log);

// race is done: a
// parallel is done: a b c
// sequence is done: a b c
```

---

#### EXECUTION CONTEXT

실행 컨텍스트(EXECUTION CONTEXT)는 단순히 코드가 실행되는 환경입니다  
JavaScript에는 Global 또는 Function의 두 가지 유형의 실행 컨텍스트가 있습니다  
각 컨텍스트에는 생성(creation) 및 실행 단계(executing phase)의 두 단계가 있습니다  
JavaScript 엔진이 코드를 읽기 시작하면 Global Execution Context라는 것이 생성됩니다.

> Global Execution Context (this === window :true)

- Creation Phase

  1. global object(window) created
  2. initializes this keyword to global

- Executing Phase (actually run your code)

  3. Variable Environment created - memory space for var variables and functions created
  4. initializes all variables to undefined (also known as hoisting) and places them with any functions into memory

  ```js
  this;
  window;
  this === window;

  // Window {...}
  // Window {...}
  // true
  ```

> Function Execution Context 함수가 호출 될 때만 함수 실행 컨텍스트가 생성됩니다.

- Creation Phase

  1. argument object created with any arguments
  2. initializes this keyword to point called or to the global object if not specified

- Executing Phase

  3. Variable Environment created - memory space for variable and functions created
  4. initializes all variables to undefined and places them into memory with any new functions

  ```js
  // Function Execution Context creates arguments object and points 'this' to the function
  function showArgs(arg1, arg2) {
    console.log('arguments: ', arguments);
    return `argument 1 is: ${arg1} and argument 2 is: ${arg2}`;
  }

  showArgs('hello', 'world');

  // arguments: { 0: 'hello', 1: 'world' }
  // argument 1 is hello and argument 2 is world

  function noArgs() {
    console.log('arguments: ', arguments);
  }

  noArgs();

  // arguments: {}
  // even though there are no arguments, the object is still created
  ```

  The keyword arguments can be dangerous to use in your code as is. In ES6, a few methods were introduced that can help better use arguments.

  ```js
  function showArgs(arg1, arg2) {
    console.log('arguments: ', arguments);
    console.log(Array.from(arguments));
  }

  showArgs('hello', 'world');

  // arguments: { 0: 'hello', 1: 'world' }
  // [ 'hello', 'world' ]

  function showArgs2(...args) {
    console.log(console.log('arguments: ', args));
    console.log(Array.from(arguments));
    return `${args[0]} ${args[1]}`;
  }

  showArgs2('hello', 'world');

  // arguments: [ 'hello', 'world' ]
  // [ 'hello', 'world' ]
  // hello world
  ```

---

#### Arrow Functions..💖

어떤 사람들은 화살표 함수를 일반 함수에 대한 syntactic sugar라고 생각하지만 화살표 함수는 일반 함수와 약간 다르게 작동합니다.  
일반 함수에 대한 간결한 대안이지만 this, arguments, super 또는 new.target 키워드에 대한 자체 바인딩이 없습니다.  
화살표 함수는 생성자로 사용할 수 없으며 메서드에 대한 best option이 아닙니다.

````js
var obj = {
  // does not create a new scope
  i: 10,
  b: () => console.log(this.i, this),
  c: function () {
    console.log(this.i, this);
  },
};

obj.b(); // prints undefined, Window {...} (or the global object)
obj.c(); // prints 10, Object {...}```
````

---

#### HOISTING

var hoisting (move declaration from bottom to top)

```js
// var (don't ever use this!)
console.log(age); // 값을 할당하기전에 출력 undefined
age = 4; // 선언도 하기전에 값을할당
console.log(age); // 4
var age;

name = 4; // ReferenceError 선언전에 값을할당
let name;
```

```js
{
  age = 4;
  var age; // var는 Block scope도 무시
}
console.log(age); // 4
```

> Avoid hoisting when possible. It can cause memory leaks and hard to catch bugs in your code. Use let and const as your go to variables.

---

#### LEXICAL ENVIRONMENT

lexical environment은 기본적으로 엔진이 현재 코드를 읽고있는 범위 또는 환경입니다.  
A new lexical environment is created when curly brackets {} are used, even nested brackets {{...}} create a new lexical environment.  
The execution context tells the engine which lexical environment it is currently working in and the lexical scope determines the available variables.

```js
function one() {
  var isValid = true; // local env
  two(); // new execution context
}

function two() {
  var isValid; // undefined
}

var isValid = false; // global
one();

/* 
   two() isValid = undefined
   one() isValid = true
   global() isValid = false
   ------------------------
   call stack
*/
```

---

#### SCOPE CHAIN

![Image Description](https://images.ctfassets.net/aq13lwl6616q/orTo9ia4TX3L5lXsW66rQ/575a4a80639a05791175fbfbd6af5826/scope_graph.png)

Each environment context that is created has a link outside of its lexical environment called the scope chain.  
The scope chain gives us access to variables in the parent environment.

---

#### Function And Block scope

```js
let globalName = 'global name'; // Global scope
{
  // Block scope
  let name = 'ellie';
  console.log(name);
  name = 'hello';
  console.log(name);
  console.log(globalName); // global name
}
console.log(name); // 아무것도 안나옴.
console.log(globalName); // global name
```

---

#### 중첩된 함수에서 자식의함수가 부모함수에 정의된 변수들에 접근이가능한 것들이 클로져

#### 스코프는 변수를찾는 규칙의집합

#### 렉시컬스코프, 글로벌스코프, 스코프체인, Function scope 와 Block scope

#### 렉시컬환경

#### THIS와Bind

#### 타입(JS런타임때 결정)

#### 클로저활용

#### 프로토타입

#### Object Oriented Programming

#### Functional Programming

#### Asyn

#### 모듈

#### Error Handling

#### 자료구조 in JS

---

#### Http, Https, Web APIs, 브라우저좌표

#### defer, DOMContentLoaded, load, unload

#### HTMLrequest->DOM,CSSOM->RenderTree->layout->paint(레이어단위로 페인트를준비)->composition

#### Bubbling & (capturing) event.stop(Immediate)Propagation ❌ ,

#### 이벤트위임

#### 스택(함수들의 실행순서와 어디로 돌아가야하는지의 정보를담고있다) = LIFO(last in first out)

#### 힙(오브젝트를 생성하거나 데이터를 만들때 그데이터들이 저장되는 공간)

#### JS가 실행되는 런타임환경(실행환경) 위에서는 멀티쓰레딩과 이벤트루프를 이용할수있다

### JS + 브라우저 런타임(실행환경)⭐

#### JS엔진에는메모리힙(할당된데이터들이저장) + 콜스택(함수들이 호출하는 순서를 기억했다가 함수가끝나면 원래있던자리로 돌아가기위해 쓰이는 LIFO자료구조)로 구성됨.

#### 웹APIs는 TaskQueue(FIFO)에 콜백함수를 넣어준다 -> 이벤트루프가 TaskQueue와 콜스택을 관찰하며 콜스택이 비워지면 콜스택에 TaskQueue에있는 콜백함수를 넣어준다.

#### MicroTaskQueue 에는 Promise의then 등록된콜백과 mutaionObserver에 등록된 콜백이들어온다

#### Render는 브라우저에서 변형한코드가 주기적으로 업데이트되기위해 주기적으로 호출되는 순서인데 그전에 requestAnimationFrame이라는 api를 부르면 그때등록한 콜백은requestAnimationFrameQueue에 차곡차곡 쌓인다

#### 클래스안에있는 함수를 다른콜백으로 전달해줄때는 클래스정보가 무시되므로 함수를 클래스와 Binding해주거나 화살표함수

#### Builder Pattern

#### Prototype💖 프로토타입(Inheritance:상속을 구현 할수있다, 속성과 함수들을 정의)을 기반으로해서 객체지향프로그래밍과 코드재사용 할수있다

#### behavior reuse 행동재사용(Inheritance) by reusing existing objects(기존에있는 오브젝트 재사용)

#### THIS🧡

#### JS이외의 다른 OOP언어에서 THIS는 클래스자신이지만 JS에서 THIS란 만들어진객체 자기자신을가리키는것이 아닌 누가부르냐에따라(호출부) 달라진다.

#### JS는 THIS라는 정보를담은 함수를 다른곳으로 할당하는순간 잃어버릴수있기때문에 Bind 또는 Class안에서 Arrow함수로 선언해줌에따라 선언될당시 스코프의 This Context를 유지한다

#### Modules💘이란 파일안에 코드를 모듈화해서 작성하는것/ 한 모듈 = 한 파일안에 작성되어있는 코드/ 모듈화해서 작성하지않으면 여러가지 파일들이있는경우 모든코드들은 글로벌스코프로 측정된다/ export, import 활용
