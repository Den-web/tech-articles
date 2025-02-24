# TimeLimitedCache Implementation in JavaScript

## Overview
This document provides a JavaScript implementation of a **Time-Limited Cache**, which stores key-value pairs that expire after a specified duration. The cache supports three main operations:

1. **set(key, value, duration)** - Stores a key-value pair with an expiration time.
2. **get(key)** - Retrieves the value if the key exists and is not expired.
3. **count()** - Returns the count of non-expired keys.

## Implementation
```javascript
var TimeLimitedCache = function() {
    this.cache = new Map();
};

/** 
 * @param {number} key
 * @param {number} value
 * @param {number} duration time until expiration in ms
 * @return {boolean} if un-expired key already existed
 */
TimeLimitedCache.prototype.set = function(key, value, duration) {
    const now = Date.now();  // Current timestamp in milliseconds
    
    const exists = this.cache.has(key) && this.cache.get(key).expiry > now;
    
    this.cache.set(key, { value, expiry: now + duration });
    
    return exists;
};

/** 
 * @param {number} key
 * @return {number} value associated with key or -1 if expired/not found
 */
TimeLimitedCache.prototype.get = function(key) {
    const now = Date.now();
    if (this.cache.has(key)) {
        const entry = this.cache.get(key);
        if (entry.expiry > now) {
            return entry.value;
        } else {
            this.cache.delete(key);  // Remove expired key
        }
    }
    return -1;  // Return -1 if key is expired or not found
};

/** 
 * @return {number} count of non-expired keys
 */
TimeLimitedCache.prototype.count = function() {
    const now = Date.now();
    let validKeys = 0;

    for (const [key, entry] of this.cache.entries()) {
        if (entry.expiry > now) {
            validKeys++;
        } else {
            this.cache.delete(key);  // Clean up expired keys
        }
    }
    return validKeys;
};
```

## Example Usage
```javascript
const timeLimitedCache = new TimeLimitedCache();
console.log(timeLimitedCache.set(1, 42, 1000)); // false (new key)
console.log(timeLimitedCache.get(1)); // 42
console.log(timeLimitedCache.count()); // 1 (before expiration)

setTimeout(() => {
    console.log(timeLimitedCache.get(1)); // -1 (expired)
}, 1100);
```

## Explanation
- **`set(key, value, duration)`**
  - Returns `true` if the key already exists and is not expired.
  - Otherwise, stores the key-value pair and returns `false`.
  
- **`get(key)`**
  - Returns the value if the key is present and not expired.
  - If expired, removes the key and returns `-1`.
  
- **`count()`**
  - Iterates through the cache and removes expired keys.
  - Returns the number of non-expired keys.

## Time Complexity
| Function | Complexity |
|----------|------------|
| `set()`  | **O(1)** |
| `get()`  | **O(1)** |
| `count()`| **O(n)** |

## Summary
This implementation provides an **efficient way to store temporary data** with expiration, ensuring **fast lookups** and **automatic cleanup**. Let me know if you need further improvements! 🚀
