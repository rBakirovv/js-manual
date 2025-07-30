# Материал для подготовки к собеседованиям 

## Содержание

[1. Основы JavaScript](#основы-javascript)

[1.1 Типы данных](#типы-данных)

[1.2 Сравнение значений](#cравнение-значений)

[1.3 Область видимости (Scope) и Hoisting](#область-видимости)

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
