2625. Flatten Deeply Nested Array Solved Medium Companies

### Corrected Code:
```javascript
var flat = function (arr, n) {
    if (n === 0) return arr; // If depth is 0, return original array

    let result = [];

    function flatten(arr, depth) {
        for (let i of arr) {
            if (Array.isArray(i) && depth > 0) {
                flatten(i, depth - 1);
            } else {
                result.push(i);
            }
        }
    }

    flatten(arr, n);
    return result;
};
```
### Explanation:
- If `n === 0`, return the array unchanged.
- Use a helper function `flatten(arr, depth)` to recursively flatten the array.
- Push elements to `result`, flattening nested arrays only if `depth > 0`.

#### Example Usage:
```javascript
console.log(flat([1, [2, [3, [4]], 5]], 2)); 
// Output: [1, 2, 3, [4], 5]
```

This ensures that the array is flattened up to `n` levels correctly. 🚀
