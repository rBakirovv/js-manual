# Материал для подготовки к собеседованиям 

## Содержание

[1. Основы JavaScript](#основы-javascript)

[1.1 Типы данных](#типы-данных)

## Основы JavaScript

### Типы данных

__Примитивы vs Объекты__

Примитивы: number, string, boolean, null, undefined, symbol, bigint.

Особенности:

- Неизменяемы (immutable).

- Передаются по значению (копируются).

- typeof null === 'object' (исторический баг).

Объекты: Object, Array, Function, Date, RegExp и др.

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
