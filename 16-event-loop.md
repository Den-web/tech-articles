### **Event Loop в Node.js vs. в браузере (Frontend)** 🚀

Event Loop (цикл событий) — это механизм, который управляет выполнением кода, обработкой событий и асинхронными задачами в JavaScript. **Но в Node.js и в браузере Event Loop работает по-разному!** ⚡

---

## **1. Общие принципы Event Loop**
И в **Node.js**, и в **браузере** JavaScript выполняется **в одном потоке** (single-threaded), а все асинхронные операции (таймеры, I/O, HTTP-запросы) обрабатываются через Event Loop.

Однако разница в **приоритете и порядке обработки задач**.

---

## **2. Event Loop в браузере (Frontend)**
В браузере есть **шесть фаз Event Loop**:
1. **Microtasks** (Promises, MutationObserver).
2. **Macrotasks** (setTimeout, setInterval, setImmediate, I/O).
3. **Render Phase** (отрисовка обновлений UI).
4. **Input Events** (клавиатура, мышь).
5. **Animations (requestAnimationFrame)**.
6. **Idle Callbacks (requestIdleCallback)**.

👉 **Главная особенность**: браузер **перерисовывает UI** после обработки микротасков, но перед макротасками.

### **Пример Event Loop в браузере**
```js
console.log("Start");

setTimeout(() => console.log("Macrotask: setTimeout"), 0);

Promise.resolve().then(() => console.log("Microtask: Promise"));

console.log("End");
```
**Вывод в браузере**:
```
Start
End
Microtask: Promise
Macrotask: setTimeout
```
**Почему?**  
- Сначала выполняется весь синхронный код.
- Потом **Microtasks (Promise)**.
- Потом **Macrotasks (setTimeout)**.
- После этого происходит **отрисовка UI**.

---

## **3. Event Loop в Node.js**
Event Loop в Node.js состоит из **шести фаз**:
1. **Timers** (`setTimeout`, `setInterval`).
2. **Pending Callbacks** (обратные вызовы I/O операций).
3. **Idle, Prepare** (внутренние задачи Node.js).
4. **Poll** (основная фаза, где обрабатываются входящие I/O запросы).
5. **Check** (`setImmediate`).
6. **Close Callbacks** (например, `socket.on("close")`).

👉 **Главное отличие**: в Node.js **Render-фаза отсутствует** и приоритеты задач другие.

---

## **4. Разница между Event Loop в Node.js и браузере**
| Фича | Node.js | Браузер |
|--------|---------|---------|
| **Основной процесс** | Серверный (работает без UI) | Интерфейсный (обновляет UI) |
| **Фаза Render** | ❌ (нет отрисовки) | ✅ (перерисовывает DOM) |
| **setTimeout vs setImmediate** | `setImmediate` выполняется раньше `setTimeout` | `setTimeout` выполняется в macrotask |
| **I/O операции** | Приоритет в `poll phase` | Обрабатываются после макрозадач |
| **requestAnimationFrame** | ❌ (нет анимаций) | ✅ (для плавных анимаций) |

---

## **5. Пример: Разница в приоритете задач**
### **В браузере**
```js
console.log("Start");

setTimeout(() => console.log("Macrotask: setTimeout"), 0);
Promise.resolve().then(() => console.log("Microtask: Promise"));
requestAnimationFrame(() => console.log("Render: requestAnimationFrame"));

console.log("End");
```
**Вывод в браузере**:
```
Start
End
Microtask: Promise
Render: requestAnimationFrame
Macrotask: setTimeout
```
🔹 **requestAnimationFrame выполняется перед макротасками, но после микротасков!**  

---

### **В Node.js**
```js
const fs = require("fs");

console.log("Start");

setTimeout(() => console.log("Timers: setTimeout"), 0);
setImmediate(() => console.log("Check: setImmediate"));

fs.readFile(__filename, () => {
    console.log("I/O: File Read");
    process.nextTick(() => console.log("Microtask: nextTick"));
    Promise.resolve().then(() => console.log("Microtask: Promise"));
});

console.log("End");
```
**Вывод в Node.js**:
```
Start
End
I/O: File Read
Microtask: nextTick
Microtask: Promise
Check: setImmediate
Timers: setTimeout
```
🔹 **В Node.js `setImmediate()` выполняется раньше, чем `setTimeout()`!**  
🔹 **Микротаски (`process.nextTick()`, `Promise`) выполняются перед `setImmediate()` и `setTimeout()`.**

---

## **6. Итог: В чём главное различие?**
| **Критерий** | **Node.js** | **Браузер** |
|-------------|------------|------------|
| **Главная цель** | Серверная обработка запросов | Отрисовка UI |
| **Event Loop** | 6 фаз, без Render | 6 фаз, включая Render |
| **Микротаски** | `process.nextTick` (высший приоритет), Promises | Promises, MutationObserver |
| **Макротаски** | `setTimeout`, `setImmediate`, I/O | `setTimeout`, `setInterval` |
| **Перерисовка UI** | ❌ Нет | ✅ Есть (requestAnimationFrame) |
| **I/O операции** | Высокий приоритет | Обрабатываются позже |

---

## **Вывод**
✅ В **браузере** Event Loop ориентирован на **рендеринг и интерактивность**, а в **Node.js** — на **обработку асинхронных операций и I/O**.  
✅ В **Node.js** `process.nextTick()` выполняется **раньше Promises**, а в **браузере** у них одинаковый приоритет.  
✅ **`setImmediate()` в Node.js выполняется раньше, чем `setTimeout(0)`**, а в браузере **все таймеры обрабатываются в macrotasks.**  
✅ **В браузере есть `requestAnimationFrame`, а в Node.js — нет.**

---

### **Сколько времени занимает один цикл Event Loop в Node.js?**
В **Node.js нет фиксированного времени на один цикл Event Loop** (в отличие от браузера, где ориентировочно 16ms для рендера при 60 FPS). Время выполнения одного полного цикла Event Loop зависит от **загруженности процесса, количества задач в очереди и приоритета этих задач**.

---

## **1. Как измерить длительность одного цикла Event Loop?**
Node.js предоставляет API `performance.now()`, которое можно использовать для измерения времени выполнения одного цикла Event Loop.

### **Простой тест**
```js
const { performance } = require("perf_hooks");

function measureEventLoopCycle() {
    const start = performance.now();
    setImmediate(() => {
        const end = performance.now();
        console.log(`One Event Loop cycle took: ${(end - start).toFixed(3)} ms`);
        measureEventLoopCycle(); // Повторяем измерение
    });
}

measureEventLoopCycle();
```
### **Вывод (примерный)**
```
One Event Loop cycle took: 2.354 ms
One Event Loop cycle took: 1.928 ms
One Event Loop cycle took: 3.102 ms
```
⚡ **Вывод:** Среднее время одного цикла Event Loop в простых условиях **1-3 мс**.

---

## **2. Что влияет на время одного цикла Event Loop в Node.js?**
Время выполнения одного цикла Event Loop зависит от нескольких факторов:

### **1. Количество задач в очереди**
Если в `Microtasks Queue` (Promises, `process.nextTick()`) или `Macrotasks Queue` (`setTimeout`, I/O) **много задач**, один цикл **затянется**.

#### **Пример с перегруженным Event Loop**
```js
const { performance } = require("perf_hooks");

function heavyComputation() {
    let sum = 0;
    for (let i = 0; i < 1e8; i++) sum += i;
    return sum;
}

setInterval(() => {
    const start = performance.now();
    heavyComputation(); // Нагрузка на CPU
    console.log(`Cycle time: ${(performance.now() - start).toFixed(3)} ms`);
}, 10);
```
### **Вывод (примерный)**
```
Cycle time: 120.543 ms
Cycle time: 118.891 ms
Cycle time: 122.305 ms
```
📌 **Проблема**: Теперь один цикл Event Loop занимает **120+ мс**, блокируя обработку других задач.

🔹 **Решение**: Переносить тяжелые задачи в Worker Threads или `setImmediate()`.

---

### **2. Блокирующий код (CPU-bound)**
Если в коде есть **синхронные тяжёлые вычисления**, они могут **заморозить Event Loop**.

#### **Пример**
```js
console.log("Start");

setTimeout(() => console.log("Timer executed"), 0);

for (let i = 0; i < 1e9; i++) {} // Блокирующий код

console.log("End");
```
### **Вывод**
```
Start
End
Timer executed  // Выполнилось только после завершения блока for
```
📌 **Вывод:** Event Loop **был заблокирован** на 100+ мс.

🔹 **Решение**: Использовать `Worker Threads` или `setImmediate()`.

---

### **3. I/O операции**
I/O (например, работа с файлами, базами данных) **не блокирует Event Loop**, но влияет на его скорость.

#### **Пример**
```js
const fs = require("fs");
const { performance } = require("perf_hooks");

fs.readFile(__filename, () => {
    console.log("File read complete");
    console.log(`Time taken: ${performance.now().toFixed(3)} ms`);
});
```
📌 **Вывод:** Чтение файла занимает **1-10 мс**, но выполняется **асинхронно**.

🔹 **Решение**: Использовать **потоки (Streams)** вместо `readFile()` для больших файлов.

---

## **3. Как оптимизировать работу Event Loop в Node.js?**
✅ **Использовать асинхронные API** (`fs.promises.readFile`, `setImmediate`).  
✅ **Разносить тяжёлые вычисления на Worker Threads** (`worker_threads`).  
✅ **Использовать `process.nextTick()` только для критически важных задач**, иначе он может задерживать Event Loop.  
✅ **Не перегружать Event Loop синхронными циклами (for, while)**, вместо этого использовать **batch processing**.

---

## **4. Итог**
💡 **Время выполнения одного цикла Event Loop в Node.js варьируется от 1 до 3 мс в нормальных условиях.**  
💡 **При высокой загрузке процессами (например, тяжёлыми вычислениями) один цикл может занять 100+ мс, замедляя обработку всех остальных задач.**  
💡 **Event Loop в браузере "привязан" к рендерингу (обычно 16.67 мс на 60 FPS), а в Node.js — нет.**
