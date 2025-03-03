### **🚀 Alternative Implementations of `debounce`**  

There are multiple ways to implement **debounce** in JavaScript. Below are different variations based on various use cases:

---

## **📌 1️⃣ Standard Debounce with `clearTimeout()` (Recommended)**
✅ **Ensures only the last function call is executed**  
✅ **Cancels previous calls within the debounce window**  
✅ **Passes arguments correctly**  

```js
function debounce(fn, delay) {
    let timer;
    
    return function(...args) {
        clearTimeout(timer); // Cancel previous execution
        timer = setTimeout(() => fn(...args), delay);
    };
}

// Example Usage:
const log = debounce(console.log, 100);
log("Hello"); // Cancelled
log("Hello"); // Cancelled
log("Hello"); // Logged after 100ms
```

---

## **📌 2️⃣ Immediate Execution Debounce (`leading` mode)**
✅ **Executes the function immediately but prevents further calls within `t` ms**  
✅ **Useful when the function should run instantly but avoid rapid re-triggering**  

```js
function debounceImmediate(fn, delay) {
    let timer;
    let isFirstCall = true;

    return function(...args) {
        if (isFirstCall) {
            fn(...args); // Execute immediately on the first call
            isFirstCall = false;
        }
        clearTimeout(timer);
        timer = setTimeout(() => {
            isFirstCall = true; // Reset for next batch of calls
        }, delay);
    };
}

// Example Usage:
const logImmediate = debounceImmediate(console.log, 200);
logImmediate("Hello"); // Immediately logged
logImmediate("Hello"); // Ignored
logImmediate("Hello"); // Ignored
setTimeout(() => logImmediate("Hello after delay"), 300); // Logged after 300ms
```

---

## **📌 3️⃣ Debounce with `Promise` for Asynchronous Functions**
✅ **Allows `async` functions to work with `debounce`**  
✅ **Useful when debouncing API calls or expensive computations**  

```js
function debounceAsync(fn, delay) {
    let timer;
    return function(...args) {
        return new Promise((resolve, reject) => {
            clearTimeout(timer);
            timer = setTimeout(async () => {
                try {
                    const result = await fn(...args);
                    resolve(result);
                } catch (error) {
                    reject(error);
                }
            }, delay);
        });
    };
}

// Example Usage:
const fetchData = debounceAsync(async (query) => {
    console.log("Fetching data for:", query);
    return `Data for ${query}`;
}, 500);

fetchData("User1");
fetchData("User2"); // "User1" request is cancelled, "User2" will be fetched after 500ms
```

---

## **📌 4️⃣ Debounce with `leading` & `trailing` Options**
✅ **Gives flexibility to trigger function on `leading` (first call) or `trailing` (last call) edge**  
✅ **Used in cases where you need control over execution timing**  

```js
function debounceAdvanced(fn, delay, options = { leading: false, trailing: true }) {
    let timer, lastArgs, shouldCall = false;

    return function(...args) {
        lastArgs = args;
        clearTimeout(timer);

        if (options.leading && !shouldCall) {
            fn(...args);
            shouldCall = true;
        }

        timer = setTimeout(() => {
            if (options.trailing) fn(...lastArgs);
            shouldCall = false;
        }, delay);
    };
}

// Example Usage:
const logAdvanced = debounceAdvanced(console.log, 300, { leading: true, trailing: false });
logAdvanced("Hello"); // Logged immediately
logAdvanced("World"); // Ignored
setTimeout(() => logAdvanced("Delayed Hello"), 400); // Logged after 400ms
```

---

### **📌 5️⃣ Debounce with `throttle` Hybrid (`leading + trailing`)**
✅ **Ensures function executes immediately & after delay if new calls are made**  
✅ **Useful for UI elements that need both immediate and delayed actions**  

```js
function debounceThrottle(fn, delay) {
    let timer, lastCallTime = 0;
    
    return function(...args) {
        const now = Date.now();
        if (now - lastCallTime > delay) {
            fn(...args); // Immediate execution
            lastCallTime = now;
        }
        
        clearTimeout(timer);
        timer = setTimeout(() => {
            fn(...args); // Execute at the end if new calls were made
        }, delay);
    };
}

// Example Usage:
const logHybrid = debounceThrottle(console.log, 200);
logHybrid("Instant Hello"); // Logged immediately
logHybrid("Delayed Hello"); // Logged after 200ms
```

---

## **📌 Summary of Implementations**
| **Type** | **Behavior** | **Use Case** |
|----------|-------------|--------------|
| **Standard Debounce** | Delays execution until `t` ms after last call | API requests, expensive computations |
| **Immediate Execution** | Runs first call immediately, then debounces | Button clicks, instant UI updates |
| **Debounce for Async** | Works with `async` functions using `Promise` | Debouncing API calls in React |
| **Advanced (Leading/Trailing)** | Custom execution on first/last call | Customizable behavior for UI events |
| **Debounce + Throttle** | Runs first call immediately, then once more | Scroll listeners, UI animations |

---

### **🚀 Which One Should You Use?**
- **Need standard debouncing?** → Use **option #1** (Standard Debounce).  
- **Want to execute the first call immediately?** → Use **option #2** (Immediate Execution).  
- **Need to debounce an async API call?** → Use **option #3** (Debounce with `Promise`).  
- **Want full control over execution?** → Use **option #4** (Leading/Trailing).  
- **Need a hybrid debounce-throttle mix?** → Use **option #5** (Debounce + Throttle).  

🚀 **Which one do you need for your project? Let me know!** 😃
