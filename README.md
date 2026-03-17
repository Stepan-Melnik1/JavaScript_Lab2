# Лабораторная работа №2 — Базовые методы работы с массивами

## Цель работы

Реализовать базовые методы работы с массивами (`forEach`, `map`, `filter`, `find`, `some`, `every`, `reduce`) **вручную**, без использования встроенных методов JavaScript. Только цикл `for`, свойство `length`, обращение по индексу и метод `push`.

---

## Содержание

1. [printArray / printArray1](#1-printarray--printarray1)
2. [forEach](#2-foreach)
3. [map](#3-map)
4. [filter](#4-filter)
5. [find](#5-find)
6. [some](#6-some)
7. [every](#7-every)
8. [reduce](#8-reduce)
9. [Контрольные вопросы](#контрольные-вопросы)

---

## 1. printArray / printArray1

### Что делает?

Две функции для вывода элементов массива в консоль — каждая в своём формате.

### Зачем два варианта?

Показывает проблему без колбэков: для каждого формата вывода приходится писать **отдельную функцию**, дублируя логику цикла. Решение этой проблемы — функция `forEach` с колбэком (см. раздел 2).

### Код

```javascript
/**
 * Выводит элементы массива в формате "Element N: value X"
 * @param {Array} array - массив для вывода
 */
function printArray(array) {
  if (!Array.isArray(array)) {
    throw new TypeError("Аргумент должен быть массивом");
  }
  for (let i = 0; i < array.length; i++) {
    console.log(`Element ${i}: value ${array[i]}`);
  }
}

/**
 * Выводит элементы массива в формате "N: X"
 * @param {Array} array - массив для вывода
 */
function printArray1(array) {
  if (!Array.isArray(array)) {
    throw new TypeError("Аргумент должен быть массивом");
  }
  for (let i = 0; i < array.length; i++) {
    console.log(`${i}:  ${array[i]}`);
  }
}
```

### Пример использования

```javascript
printArray(["яблоко", "банан", "вишня"]);
// Element 0: value яблоко
// Element 1: value банан
// Element 2: value вишня

printArray1(["яблоко", "банан", "вишня"]);
// 0:  яблоко
// 1:  банан
// 2:  вишня
```

### Как работает?

```
Массив:   ["яблоко", "банан", "вишня"]
Индексы:       0        1       2

Цикл for:
  i=0 → array[0] = "яблоко" → выводим "Element 0: value яблоко"
  i=1 → array[1] = "банан"  → выводим "Element 1: value банан"
  i=2 → array[2] = "вишня"  → выводим "Element 2: value вишня"
  i=3 → 3 < 3 = false → стоп
```

---

## 2. forEach

### Что делает?

Выполняет переданный колбэк для каждого элемента массива. **Ничего не возвращает** (`undefined`).

### Зачем нужна?

Решает проблему из раздела 1: вместо двух отдельных функций — одна универсальная. Логика вывода задаётся через колбэк:

```javascript
// Один forEach — два разных формата вывода
forEach(arr, (el, i) => console.log(`Element ${i}: value ${el}`));
forEach(arr, (el, i) => console.log(`${i}:  ${el}`));
```

### Код

```javascript
/**
 * Выполняет колбэк для каждого элемента массива.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => void
 * @returns {undefined}
 */
function forEach(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  for (let i = 0; i < array.length; i++) {
    callback(array[i], i, array);
  }
}
```

### Пример использования

```javascript
forEach([1, 2, 3], (element, index) => {
  console.log(`Element: ${element}, Index: ${index}`);
});
// Element: 1, Index: 0
// Element: 2, Index: 1
// Element: 3, Index: 2
```

### Как работает?

```
forEach([10, 20, 30], callback)
          ↓
  i=0 → callback(10, 0, [10,20,30]) → колбэк делает своё дело
  i=1 → callback(20, 1, [10,20,30]) → колбэк делает своё дело
  i=2 → callback(30, 2, [10,20,30]) → колбэк делает своё дело
  i=3 → стоп

  return undefined ← ничего не возвращаем
```

### Аргументы колбэка

| Аргумент | Что это | Обязателен? |
|----------|---------|-------------|
| `element` | Текущий элемент массива | ✅ Да |
| `index` | Индекс текущего элемента | ❌ Нет |
| `array` | Весь массив целиком | ❌ Нет |

---

## 3. map

### Что делает?

Создаёт **новый массив**, применяя колбэк к каждому элементу. Длина результата **всегда равна** длине исходного массива.

### Чем отличается от forEach?

```
forEach → просто делает что-то → возвращает undefined
map     → собирает результаты  → возвращает новый массив
```

### Код

```javascript
/**
 * Создаёт новый массив из результатов вызова колбэка для каждого элемента.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => newValue
 * @returns {Array} новый массив той же длины
 */
function map(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  const result = [];

  for (let i = 0; i < array.length; i++) {
    result.push(callback(array[i], i, array));
  }

  return result;
}
```

### Пример использования

```javascript
const numbers = [1, 2, 3];
const squared = map(numbers, (el) => el * el);
console.log(squared); // [1, 4, 9]
console.log(numbers); // [1, 2, 3] ← исходный массив не изменился!
```

### Как работает?

```
map([1, 2, 3], (el) => el * 10)

  result = []

  i=0 → колбэк(1)  → вернул 10 → result = [10]
  i=1 → колбэк(2)  → вернул 20 → result = [10, 20]
  i=2 → колбэк(3)  → вернул 30 → result = [10, 20, 30]

  return [10, 20, 30]
```

### Ключевое отличие от filter

```javascript
// map — кладёт РЕЗУЛЬТАТ колбэка
result.push(callback(array[i], i, array)); // результат преобразования

// filter — кладёт ОРИГИНАЛЬНЫЙ элемент (только если колбэк = true)
if (callback(array[i], i, array)) {
  result.push(array[i]); // сам элемент без изменений
}
```

---

## 4. filter

### Что делает?

Создаёт **новый массив** только из тех элементов, для которых колбэк вернул `true`. Длина результата **меньше или равна** длине исходного.

### Аналогия

Охранник на входе: пропускает только тех, кто прошёл проверку.

```
Все:      [1, 2, 3, 4, 5]
Проверка:  ✗  ✓  ✗  ✓  ✗  ← колбэк (чётное?)
Прошли:       [2,    4]
```

### Код

```javascript
/**
 * Возвращает новый массив из элементов, прошедших проверку колбэком.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => boolean
 * @returns {Array} новый массив (может быть короче исходного)
 */
function filter(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  const result = [];

  for (let i = 0; i < array.length; i++) {
    if (callback(array[i], i, array)) {
      result.push(array[i]);
    }
  }

  return result;
}
```

### Пример использования

```javascript
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = filter(numbers, (el) => el % 2 === 0);
console.log(evenNumbers); // [2, 4]

const words = ["кот", "слон", "як", "тигр"];
const longWords = filter(words, (el) => el.length > 3);
console.log(longWords); // ["слон", "тигр"]
```

### Как работает?

```
filter([1, 2, 3, 4, 5], (el) => el % 2 === 0)

  result = []

  i=0 → колбэк(1) → 1%2=1 → false → пропускаем
  i=1 → колбэк(2) → 2%2=0 → true  → result = [2]
  i=2 → колбэк(3) → 3%2=1 → false → пропускаем
  i=3 → колбэк(4) → 4%2=0 → true  → result = [2, 4]
  i=4 → колбэк(5) → 5%2=1 → false → пропускаем

  return [2, 4]
```

---

## 5. find

### Что делает?

Возвращает **первый элемент**, для которого колбэк вернул `true`. Если такого нет — возвращает `undefined`. После первого совпадения **сразу останавливается**.

### Чем отличается от filter?

```
filter → проходит ВЕСЬ массив → возвращает ВСЕ подходящие элементы → []
find   → останавливается на ПЕРВОМ совпадении → возвращает ОДИН элемент
```

### Код

```javascript
/**
 * Находит первый элемент, для которого колбэк возвращает true.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => boolean
 * @returns {*} первый подходящий элемент или undefined
 */
function find(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  for (let i = 0; i < array.length; i++) {
    if (callback(array[i], i, array)) {
      return array[i]; // нашли — сразу возвращаем, цикл прекращается
    }
  }

  return undefined; // ничего не нашли
}
```

### Пример использования

```javascript
const numbers = [1, 2, 3, 4, 5];
const firstEven = find(numbers, (el) => el % 2 === 0);
console.log(firstEven); // 2 ← только первое совпадение, не [2, 4]!

const notFound = find(numbers, (el) => el > 100);
console.log(notFound); // undefined
```

### Как работает?

```
find([1, 2, 3, 4, 5], (el) => el % 2 === 0)

  i=0 → колбэк(1) → false → идём дальше
  i=1 → колбэк(2) → true  → return 2 ← СТОП! дальше не идём

  Элементы 3, 4, 5 даже не проверяются — это эффективно!
```

---

## 6. some

### Что делает?

Проверяет, есть ли **хотя бы один** элемент, для которого колбэк вернул `true`. Возвращает `true` или `false`. При первом совпадении **сразу останавливается**.

### Аналогия

Вопрос: *"Есть ли в группе хоть один отличник?"* — как только нашли одного, ответ уже `true`, остальных проверять не нужно.

### Код

```javascript
/**
 * Проверяет, удовлетворяет ли хотя бы один элемент условию колбэка.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => boolean
 * @returns {boolean} true если нашёлся хоть один подходящий элемент
 */
function some(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  for (let i = 0; i < array.length; i++) {
    if (callback(array[i], i, array)) {
      return true; // нашли хоть один — достаточно!
    }
  }

  return false; // ни один не подошёл
}
```

### Пример использования

```javascript
const numbers = [1, 3, 5, 4, 7];
console.log(some(numbers, (el) => el % 2 === 0)); // true (есть 4)
console.log(some(numbers, (el) => el > 100));     // false (таких нет)
```

### Как работает?

```
some([1, 3, 5, 4, 7], (el) => el % 2 === 0)

  i=0 → колбэк(1) → false → идём дальше
  i=1 → колбэк(3) → false → идём дальше
  i=2 → колбэк(5) → false → идём дальше
  i=3 → колбэк(4) → true  → return true ← СТОП!

  Элемент 7 даже не проверяется
```

---

## 7. every

### Что делает?

Проверяет, **все ли элементы** удовлетворяют условию. Возвращает `true` только если колбэк вернул `true` для **каждого** элемента. При первом несоответствии **сразу останавливается**.

### Сравнение с some

```
some  — ищет true,  останавливается на первом true
every — ищет false, останавливается на первом false
```

### Код

```javascript
/**
 * Проверяет, удовлетворяют ли все элементы условию колбэка.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (element, index, array) => boolean
 * @returns {boolean} true если все элементы подходят, иначе false
 */
function every(array, callback) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  for (let i = 0; i < array.length; i++) {
    if (!callback(array[i], i, array)) {
      return false; // нашли несоответствие — дальше проверять смысла нет
    }
  }

  return true; // все элементы прошли проверку
}
```

### Пример использования

```javascript
console.log(every([2, 4, 6], (el) => el % 2 === 0)); // true (все чётные)
console.log(every([2, 3, 6], (el) => el % 2 === 0)); // false (3 нечётное)
```

### Как работает?

```
every([2, 4, 6], (el) => el % 2 === 0)

  i=0 → колбэк(2) → true  → !true = false  → продолжаем
  i=1 → колбэк(4) → true  → !true = false  → продолжаем
  i=2 → колбэк(6) → true  → !true = false  → продолжаем
  цикл закончился

  return true ← все прошли!

────────────────────────────────

every([2, 3, 6], (el) => el % 2 === 0)

  i=0 → колбэк(2) → true  → !true = false  → продолжаем
  i=1 → колбэк(3) → false → !false = true  → return false ← СТОП!

  Элемент 6 даже не проверяется
```

---

## 8. reduce

### Что делает?

Проходит по массиву и **накапливает результат** в переменной-аккумуляторе. Возвращает итоговое значение аккумулятора.

### Аналогия

Копилка: кидаешь монеты одну за другой, каждый раз сумма увеличивается. В конце достаёшь итог.

```
[1, 2, 3, 4, 5]  начальное значение: 0

  acc=0  + el=1 → acc=1
  acc=1  + el=2 → acc=3
  acc=3  + el=3 → acc=6
  acc=6  + el=4 → acc=10
  acc=10 + el=5 → acc=15

  return 15
```

### Код

```javascript
/**
 * Последовательно обрабатывает элементы массива, накапливая результат.
 * @param {Array} array - исходный массив
 * @param {Function} callback - функция вида (accumulator, element, index, array) => newAccumulator
 * @param {*} [initialValue] - начальное значение аккумулятора (необязательно)
 * @returns {*} итоговое значение аккумулятора
 */
function reduce(array, callback, initialValue) {
  if (!Array.isArray(array)) {
    throw new TypeError("Первый аргумент должен быть массивом");
  }
  if (typeof callback !== "function") {
    throw new TypeError("Второй аргумент должен быть функцией");
  }

  const hasInitialValue = arguments.length >= 3;

  // Граничный случай: пустой массив без начального значения
  if (array.length === 0 && !hasInitialValue) {
    return undefined;
  }

  let accumulator;
  let startIndex;

  if (hasInitialValue) {
    accumulator = initialValue; // стартуем с переданного значения
    startIndex = 0;             // обходим с первого элемента
  } else {
    accumulator = array[0];     // первый элемент становится стартовым значением
    startIndex = 1;             // обходим со второго элемента
  }

  for (let i = startIndex; i < array.length; i++) {
    accumulator = callback(accumulator, array[i], i, array);
  }

  return accumulator;
}
```

### Пример использования

```javascript
const numbers = [1, 2, 3, 4, 5];

// Сумма с начальным значением
const sum = reduce(numbers, (acc, el) => acc + el, 0);
console.log(sum); // 15

// Сумма без начального значения (первый элемент = аккумулятор)
const sum2 = reduce(numbers, (acc, el) => acc + el);
console.log(sum2); // 15

// Максимальное число
const max = reduce(numbers, (acc, el) => el > acc ? el : acc);
console.log(max); // 5

// Пустой массив без начального значения
console.log(reduce([], (acc, el) => acc + el)); // undefined
```

### Как работает с `initialValue` и без?

```
С initialValue = 0:
  acc = 0 (начинаем с 0)
  acc = callback(0, 1) = 1
  acc = callback(1, 2) = 3
  acc = callback(3, 3) = 6
  return 6

Без initialValue:
  acc = 1 (первый элемент!)
  acc = callback(1, 2) = 3  ← начинаем со второго элемента
  acc = callback(3, 3) = 6
  return 6
```

---

## Сравнение всех функций

| Функция | Возвращает | Останавливается раньше? | Зачем? |
|---------|-----------|------------------------|--------|
| `forEach` | `undefined` | ❌ Нет | Выполнить действие для каждого элемента |
| `map` | Новый массив (та же длина) | ❌ Нет | Преобразовать каждый элемент |
| `filter` | Новый массив (короче или равен) | ❌ Нет | Отобрать подходящие элементы |
| `find` | Элемент или `undefined` | ✅ На первом `true` | Найти первый подходящий |
| `some` | `boolean` | ✅ На первом `true` | Есть ли хоть один подходящий? |
| `every` | `boolean` | ✅ На первом `false` | Все ли подходят? |
| `reduce` | Любое значение | ❌ Нет | Накопить результат |

---

## Контрольные вопросы

### 1. В чём преимущества колбэков при работе с массивами?

Колбэки делают функции **универсальными**. Без колбэков для каждой задачи нужна отдельная функция — это показано в примере с `printArray` и `printArray1`. С колбэками одна функция `forEach` может и выводить в консоль, и считать сумму, и делать что угодно другое — логику задаёт тот, кто вызывает функцию.

### 2. Какие проблемы могут возникать при использовании колбэков?

- **Передача не-функции** — решается проверкой `typeof callback !== "function"` с выбросом `TypeError`
- **Передача не-массива** — решается проверкой `Array.isArray(array)`
- **"Ад колбэков"** (callback hell) — когда колбэки глубоко вложены друг в друга, код становится нечитаемым. Решается через `Promise` и `async/await`

### 3. Как реализовать функции без встроенных методов?

Через обычный цикл `for`, свойство `length` и метод `push`. Каждая функция принимает массив и колбэк, обходит массив циклом и вызывает `callback(array[i], i, array)` на каждом шаге. Отличия только в том, что делается с результатом колбэка:

```
forEach → результат колбэка игнорируется
map     → результат колбэка кладётся в result
filter  → если колбэк = true, оригинальный элемент кладётся в result
find    → если колбэк = true, возвращаем элемент и выходим
some    → если колбэк = true, возвращаем true и выходим
every   → если колбэк = false, возвращаем false и выходим
reduce  → результат колбэка становится новым аккумулятором
```
