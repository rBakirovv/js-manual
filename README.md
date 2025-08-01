# Материал для подготовки к собеседованиям 

## Содержание

[0. ООП](#ооп)

[1. Основы JavaScript](#основы-javascript)

[1.1 Типы данных](#типы-данных)

[1.2 Сравнение значений](#сравнение-значений)

[1.3 Область видимости (Scope) и Hoisting](#область-видимости)

[1.4 Контекст](#контекст)

[1.5 Прототипы и наследование](#прототипы-и-наследование)

[1.6 Асинхронность](#асинхронность)

[1.7 Map, Set, WeakMap, WeakSet](#map-set-weakmap-weakset)

[1.8 Функции](#функции)

[2 React](#react)

[2.1 Жизненный цикл компонентов](#жизненный-цикл-компонентов)

[2.2 Hooks](#hooks)

[2.3 Управление состоянием](#управление-состоянием)

[2.4 SSR](#ssr)

## ООП

__1. Инкапсуляция__

Сокрытие внутренней реализации и предоставление безопасного интерфейса.

В JavaScript реализуется через:

- Закрытые свойства (с # в ES2022+ или через замыкания)

- Геттеры/сеттеры (get/set)

```
class BankAccount {
  #balance = 0; // Приватное поле

  // Публичный интерфейс
  deposit(amount) {
    this.#balance += amount;
  }

  get balance() {
    return this.#balance;
  }
}

const account = new BankAccount();
account.deposit(100);
console.log(account.balance); // 100
// account.#balance; // Ошибка! Нельзя обратиться напрямую
```

__2. Наследование__

Создание дочерних классов на основе родительских.

В JavaScript работает через:

- Прототипы (в классическом JS)

- Ключевое слово extends (в ES6+)

```
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} издаёт звук.`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} лает!`); // Переопределение метода
  }
}

const dog = new Dog("Шарик");
dog.speak(); // "Шарик лает!"
```

__3. Полиморфизм__

Разные объекты могут использовать методы с одинаковыми именами.

```
class Cat extends Animal {
  speak() {
    console.log(`${this.name} мяукает!`);
  }
}

const animals = [new Dog("Бобик"), new Cat("Мурка")];
animals.forEach(animal => animal.speak());
// "Бобик лает!"
// "Мурка мяукает!"
```

__4. Абстракция__

Упрощение сложной реальности через выделение ключевых характеристик.

```
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error("Shape — абстрактный класс!");
    }
  }

  calculateArea() {
    throw new Error("Метод должен быть переопределён!");
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  calculateArea() {
    return Math.PI * this.radius ** 2;
  }
}

const circle = new Circle(5);
console.log(circle.calculateArea()); // 78.5398..
```

## Основы JavaScript

### Типы данных

__Примитивы vs Объекты__

__Примитивы:__ number, string, boolean, null, undefined, symbol, bigint.

Особенности:

- Неизменяемы (immutable).

- Передаются по значению (копируются).

- typeof null === 'object' (исторический баг).

__Объекты:__ Object, Array, Function, Date, RegExp и др.

Особенности:

- Изменяемы (mutable).

- Передаются по ссылке.

- Имеют прототипы (```__proto__```).

Пример на собеседование:

```
javascript
let a = { name: 'Alice' };
let b = a;
b.name = 'Bob';
console.log(a.name); // 'Bob' (изменение по ссылке)
```

Проверка типов
- typeof x – работает для примитивов, но typeof [] === 'object'.

- instanceof – проверяет цепочку прототипов ([] instanceof Array → true).

- Object.prototype.toString.call(x) – точный способ:

```
Object.prototype.toString.call([]) // "[object Array]"
Object.prototype.toString.call(null) // "[object Null]"
```

### Сравнение значений

__== vs ===__

== – нестрогое сравнение (с приведением типов).

=== – строгое сравнение (без приведения).

Опасные случаи:

```
'' == '0'          // false
0 == ''            // true ('' → 0)
false == 'false'   // false (true == 'false' → false)
null == undefined  // true (но null === undefined → false)
```

__Совет:__ Всегда используй ===, если нет явной причины для ==.

Object.is()
Аналог ===, но с отличиями:

```
Object.is(NaN, NaN)   // true (в отличие от NaN === NaN → false)
Object.is(0, -0)      // false (0 === -0 → true)
```

### Область видимости

__var, let, const__

__var:__

- Function-scoped (видна во всей функции).

- Поднимается (hoisting) с undefined.

```
console.log(x); // undefined (не ошибка!)
var x = 10;
```

__let/const:__

- Block-scoped ({}).

- Hoisting есть, но попадают в TDZ (Temporal Dead Zone) до объявления.

```
console.log(y); // ReferenceError (TDZ)
let y = 20;
```

__Temporal Dead Zone, TDZ:__

Временная мертвая зона __(Temporal Dead Zone, TDZ)__ — это особенность поведения в JavaScript, которая возникает при использовании переменных, объявленных через let и const. Она описывает период между началом блочной области видимости и моментом объявления переменной, в течение которого к ней нельзя обратиться.

Зачем нужна TDZ?

- Раннее обнаружение ошибок: Помогает выявлять ошибки, когда переменные используются до объявления.

- Улучшение качества кода: Поощряет правильное размещение переменных.

- Предсказуемость: Делает поведение let и const более последовательным по сравнению с var.

__Замыкания (Closures)__

Замыкание — это комбинация функции и лексического окружения, в котором эта функция была объявлена. Это мощная концепция JavaScript, позволяющая функциям "запоминать" переменные из внешней области видимости даже после того, как выполнение кода вышло из этой области.

__Как работают замыкания?__

Когда функция создаётся внутри другой функции, она получает доступ к:

- Своим локальным переменным

- Переменным родительской функции

- Глобальным переменным

При этом даже после завершения работы внешней функции, внутренняя функция сохраняет доступ к её переменным.

Пример:

```
function createCounter() {
  let count = 0;
  return () => ++count;
}
const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2 (count сохраняется)
```

Вопрос на собеседовании:

```
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 100); // 5,5,5,5,5
}
// Как исправить на вывод 0,1,2,3,4?
```

Ответ:

Использовать let вместо var (создаёт новую переменную на каждой итерации).

Или IIFE:

```
for (var i = 0; i < 5; i++) {
  ((j) => setTimeout(() => console.log(j), 100))(i);
}
```

### Контекст

Значение this зависит от способа вызова функции:

- Глобальный контекст: this === window (в браузере).

- Метод объекта: this – объект перед точкой.

```
const user = {
  name: 'Alice',
  greet() { console.log(this.name); }
};
user.greet(); // 'Alice'
```

Стрелочные функции: Не имеют своего this, берут из внешнего scope.

Явное указание: call, apply, bind.

__Когда что использовать?__

- Используйте call, когда знаете точное количество аргументов

- Используйте apply, когда аргументы динамические или уже находятся в массиве

- Используйте bind, когда нужно создать функцию с фиксированным контекстом для последующего вызова

```
function showName() { console.log(this.name); }
showName.call({ name: 'Bob' }); // 'Bob'
```

Вопрос: Что выведет код?

```
const obj = {
  name: 'Alice',
  greet: () => console.log(this.name)
};
obj.greet(); // undefined (стрелка берёт this из window)
```

### Прототипы и наследование

__Прототипное наследование__

- Каждый объект имеет скрытое свойство ```__proto__``` (или [[Prototype]]).

- Object.getPrototypeOf(obj) – получить прототип.

- obj.hasOwnProperty('key') – проверяет собственное свойство (не из прототипа).

Пример:

```
const animal = { eats: true };
const rabbit = { jumps: true };
rabbit.__proto__ = animal; // rabbit теперь наследует animal
console.log(rabbit.eats); // true (из прототипа)
```

__При обращении к свойству объекта JavaScript:__

- Ищет свойство в самом объекте

- Если не находит — ищет в прототипе

- Продолжает идти по цепочке прототипов до null

__Особенности прототипного наследования:__

- Динамичность: Можно изменять прототип даже после создания объектов

- Делегирование: Свойства не копируются, а доступны через ссылку

- Эффективность: Экономит память, так как методы хранятся в прототипе  

Вопрос: Как работает new?

Ответ:

- Создаётся новый пустой объект ({}).

- this связывается с этим объектом.

- Устанавливается ```__proto__``` на ClassName.prototype.

- Выполняется тело конструктора.

- Возвращается объект (если конструктор не вернул свой объект).

### Асинхронность

__Event Loop (цикл событий) — это механизм, который позволяет JavaScript обрабатывать асинхронные операции, несмотря на то, что язык является однопоточным. Он отвечает за порядок выполнения кода, обработку колбэков и взаимодействие с Web APIs__

- Call Stack – выполняет синхронный код.

- Web APIs (Окружение браузера/Node.js) – setTimeout, fetch, DOM events.

- Callback Queue (Macrotask) – setTimeout, setInterval.

- Microtask Queue – Promise.then, queueMicrotask, MutationObserver.

__Порядок выполнения:__

1. Синхронный код.

2. Микрозадачи (полностью очищаются).

3. Рендеринг (если браузеру нужно перерисовать страницу)

4. Макрозадача (одна задача).

__Promise, async/await__

- Promise – __это объект который содержит будущее значение асинхронной операции.__

- async функция всегда возвращает Promise.

- await – ждёт разрешения Promise (блокирует только текущий контекст).

__Промисы (Promise) в JavaScript могут находиться в одном из трех состояний:__

__Pending (Ожидание)__

- Начальное состояние промиса

- Промис еще не выполнен и не отклонен

- Переходит либо в fulfilled, либо в rejected

```
const pendingPromise = new Promise((resolve, reject) => {
  // Промис находится в состоянии pending
  // пока не будет вызван resolve или reject
});
```

__Fulfilled (Выполнен успешно)__

- Промис успешно завершил свою операцию

- Был вызван метод resolve()

- Цепочка продолжается через .then()

```
const fulfilledPromise = Promise.resolve('Успех!');
// или
new Promise((resolve) => resolve('Успех!'));
```

__Rejected (Отклонен)__

- Промис завершился с ошибкой

- Был вызван метод reject()

- Обрабатывается через .catch() или второй аргумент .then()

```
const rejectedPromise = Promise.reject(new Error('Ошибка!'));
// или
new Promise((resolve, reject) => reject(new Error('Ошибка!')));
```

Особенности состояний:

- Состояние неизменно - промис может перейти из pending только в fulfilled или rejected, и после этого его состояние больше не меняется.

- Обработка состояний:

```
const examplePromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    Math.random() > 0.5 ? resolve('Успех') : reject('Ошибка');
  }, 1000);
});

examplePromise
  .then(result => console.log('Успех:', result))
  .catch(error => console.log('Ошибка:', error));
```

Методы проверки состояния:

- Прямого способа проверить состояние промиса нет

- Можно использовать .then(), .catch() или Promise.race() для косвенной проверки

### Map, Set, WeakMap, WeakSet

__Map vs Object__

- Ключи в Map могут быть любого типа (в Object – только строки/symbols).

- Сохраняет порядок добавления.

- Методы: set, get, has, delete, size.

```
const map = new Map();
map.set(42, 'Ответ на всё');
map.get(42); // 'Ответ на всё'
```

__Set__

Хранит уникальные значения:

```
const set = new Set([1, 2, 2, 3]);
console.log([...set]); // [1, 2, 3]
```

__WeakMap и WeakSet__

- Ключи – только объекты.

- Не препятствуют сборке мусора (если объект удалён, он исчезнет из WeakMap).

- Не поддерживают итерацию.
  
__Использование:__

- Кеширование.

- Приватные данные в классах.

## Функции

__Функции в JavaScript__ — это объекты, которые можно вызывать для выполнения кода

__Function Declaration (Объявление функции)__

Создается до выполнения кода (поднимается, hoisting).

```
function sum(a, b) {
  return a + b;
}
console.log(sum(2, 3)); // 5
```

Особенности:

- Можно вызвать до объявления (из-за hoisting)

- Имеет собственный this и arguments

- Подходит для общего использования

__Function Expression (Функциональное выражение)__

Создается в момент выполнения кода (нет hoisting).

```
const multiply = function(a, b) {
  return a * b;
};
console.log(multiply(2, 3)); // 6
```

Особенности:

- Нельзя вызвать до объявления

- Можно передавать как аргумент (например, в setTimeout)

- Может быть анонимной (function() {}) или именованной (function namedFunc() {})

__Arrow Function (Стрелочная функция)__

Короткий синтаксис, нет своего this (берет из внешней области).

```
const divide = (a, b) => a / b;
console.log(divide(6, 3)); // 2
```

Особенности:

- Нет своего this (использует внешний контекст)

- Нет arguments (можно использовать ...rest)

- Нельзя использовать как конструктор (new ArrowFunc() — ошибка)

- Не поднимается (hoisting отсутствует)

__IIFE (Immediately Invoked Function Expression)__

Функция, которая выполняется сразу после создания.

```
(function() {
  console.log("IIFE выполнилась!");
})();
```

Особенности:

- Позволяет изолировать переменные (раньше использовалось для модулей)

- Полезна для избежания засорения глобальной области

__Generator Function (Функция-генератор)__

Может приостанавливать выполнение (yield).

```
function* countTo(n) {
  for (let i = 1; i <= n; i++) {
    yield i;
  }
}
const gen = countTo(3);
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
```

Особенности:

- Возвращает итератор

- Позволяет ленивые вычисления

## React

### Жизненный цикл компонентов

__После React 16.3 (с хуками)__

Функциональные компоненты + хуки заменили большинство случаев классовых компонентов.

Аналоги методов:

- componentDidMount → useEffect(() => {}, []).

- componentDidUpdate → useEffect(() => {}, [deps]).

- componentWillUnmount → useEffect(() => { return () => {} }, []).

### Hooks

Хуки — это специальные функции в React, которые позволяют использовать состояние (state) и другие возможности React без написания классовых компонентов.

__Основные хуки__

__useState:__

```
const [count, setCount] = useState(0);
setCount(prev => prev + 1); // Функциональное обновление
```

__useEffect:__

- Без зависимостей: выполняется при каждом рендере.

- Пустой массив: аналог componentDidMount.

- С зависимостями: аналог componentDidUpdate.

```
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => subscription.unsubscribe(); // cleanup
}, [props.source]);
```

__useMemo и useCallback:__

__useMemo__ – кеширует значение:

```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

__useCallback__ – кеширует функцию:

```
const memoizedCallback = useCallback(() => doSomething(a, b), [a, b]);
```

__useRef__ — хук для ссылок и изменяемых значений

Позволяет сохранять mutable-значение между рендерами без перерисовки.

```
import { useRef, useEffect } from 'react';

function InputFocus() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus(); // Установка фокуса при монтировании
  }, []);

  return <input ref={inputRef} type="text" />;
}
```

Правила хуков

- Не вызывать хуки в условиях/циклах (порядок вызова должен быть одинаковым).

- Использовать только в функциональных компонентах (или кастомных хуках).

__Пример кастомного хука:__

```
function useWindowSize() {
  const [size, setSize] = useState({ width: window.innerWidth, height: window.innerHeight });
  useEffect(() => {
    const handleResize = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  return size;
}
```

### Управление состоянием

React предоставляет различные способы управления состоянием приложения — от локального состояния компонентов до глобальных стейт-менеджеров.

__Локальное состояние (Local State)__ - useState, базовый хук для управления состоянием внутри компонента.

__Контекст (Context API)__

Позволяет передавать данные через дерево компонентов без пропсов:

```
import { createContext, useContext } from 'react';

const ThemeContext = createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div>Тема: {theme}</div>;
}
```

Проблемы:

- Не оптимизирован для частых обновлений

- Перерисовывает всех потребителей при изменении контекста

Когда использовать:

- Тема, локализация, редко меняющиеся данные

__Redux и Redux Toolkit (RTK)__

Redux (классический)

Архитектура Flux + иммутабельные обновления:

```
// Action
const increment = () => ({ type: 'INCREMENT' });

// Reducer
function counter(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT': return state + 1;
    default: return state;
  }
}

// Store
const store = createStore(counter);
store.dispatch(increment());
```

Проблемы классического Redux:

- Много шаблонного кода

- Сложная настройка асинхронных операций

__Redux Toolkit (RTK) — современный подход__

Упрощает Redux с помощью createSlice и configureStore:

```
import { createSlice, configureStore } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: state => state + 1,
    decrement: state => state - 1
  }
});

const store = configureStore({
  reducer: counterSlice.reducer
});

store.dispatch(counterSlice.actions.increment());
```

Плюсы RTK:

- Автоматически генерирует экшены

- Встроенный Immer для мутабельного-looking кода

- Интеграция с RTK Query для API-запросов

Когда использовать:

- Крупные приложения с сложным стейтом

- Когда нужен предсказуемый стейт-менеджмент

__MobX__

Реактивное управление состоянием через наблюдаемые объекты:

```
import { makeAutoObservable } from 'mobx';

class CounterStore {
  count = 0;

  constructor() {
    makeAutoObservable(this);
  }

  increment() {
    this.count++;
  }
}

const store = new CounterStore();
```

Использование в React (с observer):

```
import { observer } from 'mobx-react-lite';

const Counter = observer(({ store }) => (
  <button onClick={() => store.increment()}>
    {store.count}
  </button>
));
```

Плюсы:

- Минимум шаблонного кода

- Автоматическая реактивность

- Не требует редьюсеров/экшенов

Когда использовать:

- Быстрое прототипирование

- Приложения с сложными реактивными зависимостями

### SSR

__Next.js__ — это React-фреймворк с расширенными возможностями, включая серверный рендеринг (SSR), статическую генерацию (SSG) и гибридные подходы.

SSR — это процесс рендеринга React-приложения на сервере и отправки готового HTML клиенту.

Проблемы, которые решает SSR:

- SEO: Поисковые системы плохо индексируют чистый клиентский JS (например, в CRA).

- Производительность: Быстрая первая загрузка страницы (особенно на слабых устройствах).

- Социальные сети: Корректное отображение превью при расшаривании (OpenGraph, Twitter Cards).

__Реализация SSR в Next.js__

__getServerSideProps (основной метод SSR)__

Функция, которая выполняется на сервере при каждом запросе и передает данные в компонент.

```
// pages/post/[id].js
export async function getServerSideProps(context) {
  // Параметры из URL (/post/123 → context.params.id = '123')
  const { id } = context.params;
  
  // Запрос к API или БД
  const res = await fetch(`https://api.example.com/posts/${id}`);
  const post = await res.json();

  // Возвращаем данные как props для страницы
  return { 
    props: { post } 
  };
}

function PostPage({ post }) { // Данные приходят из getServerSideProps
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

Особенности:

- Выполняется только на сервере (код не попадает в клиентский бандл).

- Имеет доступ к req (запрос) и res (ответ) из Node.js.

- Подходит для данных, которые часто меняются (лента новостей, пользовательский контент).

__SSR + API Routes__

Next.js позволяет создавать API endpoints прямо в проекте:

```
// pages/api/post.js
export default function handler(req, res) {
  const { id } = req.query;
  // Здесь можно подключиться к БД
  res.status(200).json({ id, title: 'Статья о Next.js' });
}
```

Использование в getServerSideProps:

```
export async function getServerSideProps(context) {
  const { id } = context.params;
  const res = await fetch(`http://localhost:3000/api/post?id=${id}`);
  const post = await res.json();
  return { props: { post } };
}
```

__Минусы SSR:__

- Медленный TTFB (Time To First Byte) — сервер тратит время на рендеринг.

- Нагрузка на сервер — каждый запрос требует вычислений.

- Сложнее кэшировать чем SSG.

__Идеальные сценарии для SSR:__

- Личные кабинеты (данные индивидуальны для каждого пользователя)

- Новостные ленты (частые обновления)

- Страницы с динамическими мета-тегами (соц. сети)
