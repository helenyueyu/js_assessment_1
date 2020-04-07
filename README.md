# js_assessment_1


### Symmetric Substrings ###
```js
// helper method that returns a boolean of whether the string is a palindrome 
const isPalindrome = function(s) {
    return s === s.split('').reverse().join('');
}


// remember: slice is exclusive!
String.prototype.symmetricSubstrings = function() {
    let res = []; 
    for (let i = 0; i <= this.length-1; i++) {
        for (let j = i+1; j <= this.length; j++) {
            res.push(this.slice(i, j)); 
        }
    }
    // res by default contains all substrings: we filter by constraints (palindrome && length > 1), 
    // and then alphabetically sort 
    return res.filter(x => isPalindrome(x) && x.length > 1).sort(); 
}
```

### Real Words in String ###

```js

String.prototype.realWordsInString = function(dictionary) {
    // res contains all substrings 
    let res = []; 
    for (let i = 0; i <= this.length-1; i++) {
        for (let j = i+1; j <= this.length; j++) {
            res.push(this.slice(i, j)); 
        }
    }
    
    // create a new set to avoid duplicates 
    let words = new Set();  
    for (let i = 0; i < res.length; i++) {
        if (dictionary.includes(res[i])) {
            words.add(res[i]); 
        }
    }    
    
    // convert the set back into an array, then sort alphabetically 
    return Array.from(words).sort(); 
}
```

```js
// alternative approach (without using set) 
String.prototype.realWordsInString = function(dictionary) {
    // res contains all substrings 
    let res = []; 
    for (let i = 0; i <= this.length-1; i++) {
        for (let j = i+1; j <= this.length; j++) {
            res.push(this.slice(i, j)); 
        }
    }
    
    let words = []; 
    // create a new set to avoid duplicates 
    for (let i = 0; i < res.length; i++) {
        if (dictionary.includes(res[i]) && !words.includes(res[i])) {
            words.push(res[i]); 
        }
    }    
    
    // convert the set back into an array, then sort alphabetically 
    return words.sort(); 
}
```

### Titleize ###

```js
// helper method that determines whether a word should be capitalized 
function capitalize(s, idx) {
    let littleWords = ["a", "and", "of", "over", "the"]; 
    return idx === 0 || !littleWords.includes(s) ? s[0].toUpperCase() + s.slice(1) : s; 
}

// split the string into words (recall map takes index as an optional second argument) 
function titleize(str) {
    return str.split(' ').map((x, idx) => capitalize(x, idx)).join(' '); 
}

```


### Anagrams ###

```js
// single-hash solution: iterate through each character in the first string to create a frequency hash 
// e.x. for str1 = "happy", and str2 = "pypah"

function anagrams(str1, str2) {
    let h = {}; 
    for (let i = 0; i < str1.length; i++) {
        if (h[str1[i]] === undefined) {
            h[str1[i]] = 1; 
        } else {
            h[str1[i]]++; 
        }
    }
    // at this point, h = {h: 1, a: 1, p: 2, y: 1}
    for (let i = 0; i < str2.length; i++) {
        if (h[str2[i]] === undefined) {
            // the character str2[i] doesn't exist in the first string, so we know both strings aren't anagrams
            return false; 
        } else {
            // decrement the frequency of letter str2[i] 
            h[str2[i]]--; 
        }
    }
    // when we're done, we check to make sure that every element has their frequency reduced to 0 
    // (this should happen since we increment if the character is in str1, and decrement if the character is in str2)
    return Object.values(h).every(x => x === 0); 
}
```


```js
// non-hash solution

An alternative solution is to use a custom sort method (like writing out their own bubbleSort), and then transforming both strings into arrays of characters, sorting both characters using their custom bubbleSort (remember that they shouldn't use the built-in sort() method). However, when comparing arrays in Javascript, one shouldn't ever compare them directly, as their objectIds are different. So something like `[1, 2, 3] === [1, 2, 3]` will always return false. Instead, we want to JSON.stringify the output, so `JSON.stringify([1, 2, 3]) === JSON.stringify([1, 2, 3])` would return true.  
```


### Jumble Sort ###

```js
// the below implementation is basically using BubbleSort 
function jumbleSort(string, alphabet = 'abcdefghijklmnopqrstuvwxyz'.split('')) {
    // give a default chars array if alphabet argument isn't provided 
    let c = string.split(''); 
    let sorted = false; 
    while (!sorted) {
        sorted = true; 
        for (let i = 0; i < c.length-1; i++) {
            if (alphabet.indexOf(c[i]) > alphabet.indexOf(c[i+1])) {
                // .indexOf() method in Javascript returns the first index an element appears in 
                [c[i], c[i+1]] = [c[i+1], c[i]]; 
                sorted = false; 
            }
        }
    }
    return c.join(''); 
}

```


### Binary Search ###

```js


function binarySearch(sortedArray, target) {
    // the base case: we can't ever find a target in an empty array!
    if (sortedArray.length === 0) return -1; 

    let mid = Math.floor(sortedArray.length / 2); 
    let guess = sortedArray[mid]; 
    
    if (guess === target) return mid;  

    // remember that slice is exclusive! (we don't want to include the mid) 
    let left = sortedArray.slice(0, mid); 
    let right = sortedArray.slice(mid+1); 
    
    if (target < guess) {
        return binarySearch(left, target); 
    } else {
        let attempt = binarySearch(right, target); 
        // remember that attempt will return the index w.r.t. right half of the array
        // so we need to remember to add `mid + 1` (also attempt might not exist)
        return attempt === -1 ? -1 : mid + 1 + attempt; 
    }
}

```

```js

Array.prototype.bubbleSort = function(callback) {
    // .slice(0) makes a shallow duplicate (sufficient for our case, 
    // because we only have a 1-dimensional array 
    let copy = this.slice(0); 

    // we define a defaultCallback to use if no callback is passed in 
    function defaultCallback(a, b) {
        return a < b ? -1 : 1; 
    }
    if (callback === undefined) {
        callback = defaultCallback; 
    }
    let sorted = false; 
    while (!sorted) {
        sorted = true; 
        for (let i = 0; i < copy.length-1; i++) {
            // the callback will return 1 if a "is less than" b, measuring by the callback 
            if (callback(copy[i], copy[i+1]) === 1) {
                [copy[i], copy[i+1]] = [copy[i+1], copy[i]]; 
                sorted = false; 
            }
        }
    }
    return copy; 
}

```


### Quick Sort ###

```js

Array.prototype.quickSort = function(callback) {
    // base case 
    if (this.length === 0) return []; 

    function defaultCallback(a, b) {
        return a < b ? -1 : 1;
    }
    if (callback === undefined) {
        callback = defaultCallback;
    }
    
    // pivot can be any element in our array, but for simplicity sake, we'll take the first one 
    let pivot = this.shift(); 
    let left = []; 
    let right = []; 
    
    // we're going to throw elements in our array into left & right buckets, depending on whether
    // its smaller than or larger than our pivot (which is determined by our callback function) 
    for (let i = 0; i < this.length; i++) {
        if (callback(this[i],pivot) === -1) {
            left.push(this[i]); 
        } else {
            right.push(this[i]); 
        }
    }
    
    // finally, assuming that the `left` subarray contains elements smaller than the pivot
    // and the `right` subarray contains larger elements, we want to concatenate the left, pivot, and
    // right together 
    return left.quickSort(callback).concat([pivot]).concat(right.quickSort(callback)); 
}
```


### Merge Sort ###

```js
Array.prototype.mergeSort = function(callback) {
    function defaultCallback(a, b) {
        return a < b ? -1 : 1;
    }
    if (callback === undefined) {
        callback = defaultCallback;
    }
    
    // base case 
    if (this.length === 0 || this.length === 1) return this; 

    let mid = Math.floor(this.length / 2); 
    let left = this.slice(0, mid); 
    let right = this.slice(mid); 

    return merge(left.mergeSort(callback), right.mergeSort(callback), callback); 
}

function merge(a, b, callback) {
    let res = []; 
    while (a.length > 0 && b.length > 0) {
        if (callback(a[0], b[0]) === -1) {
            res.push(a.shift()); 
        } else {
            res.push(b.shift()); 
        }
    }
    // between a and b, either both will be empty, or exactly one will be non-empty
    // if either a or b or nonempty, the elements will be the largest elements, and sorted 
    return res.concat(a).concat(b); 
}
```


### Recursive Exponent ###

```js
function exponent(b, n) {
    if (n === 0) return 1; 
    if (n < 0) {
        // if the exponent is negative, we want to add (to reach our base case of 0) 
        // remember to multiply b in the denominator as our recursive step 
        return 1/b*exponent(b, n+1)
    } else {
        return b*exponent(b, n-1); 
    }
}
```


### Factorial Recursion ###

```js
function factorialsRec(num) {
    if (num === 1) {
        return [1]; 
    } else {
        let prev = factorialsRec(num-1); 
        return prev.concat([(num-1)*prev[prev.length-1]]); 
    }
}
```

### Digital Root ###

```js
function digitalRoot(num) {
    if (num <= 9) return num; 
    while (num > 9) {
        num = digitalRootStep(num); 
    }
    return num; 
}

function digitalRootStep(n) {
    let arr = []; 
    while (n > 9) {
        // get the last digit
        let digit = n % 10; 
        arr.push(digit); 
        
        // remove the last digit 
        n = Math.floor(n / 10); 
    }
    // n is now a one digit number, but remember, we STILL need to push the last digit!
    arr.push(n); 
    return arr.reduce((a,b) => a + b); 
}
```

### Deep Dup ###

```js
function deepDup(arr) {
    let res = []; 
    for (let i = 0; i < arr.length; i++) {
        // recursively call deepDup if the element is an array
        if (Array.isArray(arr[i])) {
            res.push(deepDup(arr[i])); 
        } else {
            res.push(arr[i]); 
        }
    }
    return res; 
}
```

### Fibs Sum ###

```js
// helper method that returns the nth Fibonacci number 
function fibonacci(n) {
    if (n === 1) {
        return 1; 
    } else if (n === 2) {
        return 1; 
    } else {
        return fibonacci(n-2) + fibonacci(n-1); 
    }
}

function fibsSum(n) {
    if (n === 1) {
        return 1; 
    } else {
        return fibsSum(n-1) + fibonacci(n)
    }
}
```

### String Include Key ###

```js
/*
E.g. string: apple, key: ale

StringInclude("apple", "ale")
1) f1 = 'a', f2 = 'a' 
    first keys match, cut off the first elements for both
    stringInclude("pple", "le") 
2) f1 = 'p', f2 = 'l' 
    keys don't match, only remove the first letter of the string
    stringInclude("ple", "le") 
3) f1 = 'p', f2 = 'l' 
    stringInclude("le", "le")
4) f1 = 'l', f2 = 'l' 
    stringInclude("e", "e")
5) f1 = 'e', f2 = 'e' 
    stringInclude("", "") => we return true, since key.length = 0!
*/

function stringIncludeKey(string, key) {
    if (string.length === 0 && key.length > 0) return false; 
    if (key.length === 0) return true; 
    
    let f1 = string[0]; 
    let f2 = key[0]; 
    
    if (f1 === f2) {
        return stringIncludeKey(string.slice(1), key.slice(1)); 
    } else {
        return stringIncludeKey(string.slice(1), key); 
    }
}
```


### Recursive Sum ###

```js
function recSum(numArr) {
    if (numArr.length === 0) return 0; 
    return recSum(numArr.slice(0, numArr.length-1)) + numArr[numArr.length-1]; 
}
```

### First Even Numbers Sum ###

```js
function firstEvenNumbersSum(n) {
    if (n === 1) return 2; 
    return firstEvenNumbersSum(n-1) + 2*n; 
}

```


### myReverse ###

```js
// iterate from the end of the array to add itmes 
function myReverse(array) {
    let res = []; 
    for (let i = array.length-1; i >= 0; i--) {
        res.push(array[i]);
    }
    return res; 
}
```

### Median ###

```js
/* 
x = [1, 2, 3, 4] => avg(2, 3)
x = [1, 2, 3] => 2 
*/

Array.prototype.median = function() {
    // remember to sort the array first 
    let arr = this.slice(0).sort((a,b) => a < b ? -1 : 1); 
    
    if (arr.length === 0) return null; 
    let mid = Math.floor(arr.length / 2); 
    
    if (arr.length % 2 === 0) {
        return (arr[mid-1] + arr[mid]) / 2; 
    } else {
        return arr[mid]; 
    }
}
```

### First N Primes ###

```js

function primes(num) {
    let arr = []; 
    let i = 2; 
    while (arr.length < num) {
        if (isPrime(i)) {
            arr.push(i); 
        }
        i++; 
    }
    return arr; 
}

function isPrime(n) {
    if (n === 1) return false; 
    for (let i = 2; i < n; i++) {
        if (n % i === 0) {
            return false; 
        }
    }
    return true; 
}

```

### myFind ###

```js
function myFind(array, callback) {
    for (let i = 0; i < array.length; i++) {
        if (callback(array[i])) {
            // return the first time the callback returns true!
            return array[i]; 
        }
    }
    return undefined; 
}
```

### myFlatten ###

```js
// the base case is where we have a singly-nested array, like: [1, 2, 3, 4]
// in this situation, we can loop through that array and concat each element to our result

Array.prototype.myFlatten = function() {
    let res = []; 
    for (let i = 0; i < this.length; i++) {
        // can also write `this[i] instanceof Array` 
        if (Array.isArray(this[i])) {
            // the .myFlatten function is ALWAYS going to return an array 
            // so to avoid nesting, we don't want to push, we want to concat 
            res = res.concat(this[i].myFlatten()); 
        } else {
            res = res.concat([this[i]]); 
        }
    }
    return res; 
}
```

### Factors ###

```js
function factors(num) {
    let res = []; 
    for (let i = 1; i <= num; i++) {
        if (num % i === 0) res.push(i); 
    }
    return res; 
}
```


### Transpose ###

```js
/*
transpose([
 [1, 2], 
 [3, 4], 
 [5, 6]
]) = [
 [1, 3, 5], 
 [2, 4, 6]
] 

For the length of one of the inner arrays in the input, (e.g. [1, 2] has length 2), 
we're going to iterate over and map all the elements at a particular index. So at i = 0, we have: 
[[1, 2], [3, 4], [5, 6]].map(x => x[0]) // gives us: [1, 3, 5]
[[1, 2], [3, 4], [5, 6]].map(x => x[1]) // gives us: [2, 4, 6]

We push these outputs into our res array. 
*/

function transpose(arr) {
    let res = []; 
    // loop over the length of one of the inner elements 
    for (let i = 0; i < arr[0].length; i++) {
        res.push(arr.map(x => x[i])); 
    }
    return res; 
}
```

### myRotate ###

```js
Array.prototype.myRotate = function(times=1) {
    // negativeFlag tells us which direction we need to rotate 
    let negativeFlag = times < 0 ? 1 : -1; 
    // absolute-valueify the times variable, so we can simplify our while loop (decrement only) 
    times = times > 0 ? times : -1 * times; 
    while (times > 0) {
        // this.shift(), this.unshift(), this.pop(), and this.push() are all mutating!
        negativeFlag === -1 ? this.push(this.shift()) : this.unshift(this.pop()); 
        times--; 
    }
    return this; 
}
```


### mySlice ###

```js
String.prototype.mySlice = function(startIdx, endIdx=this.length) {
    // set the endIdx to the length of the string as a default argument 
    let res = ""; 
    for (let i = startIdx; i < Math.min(this.length, endIdx); i++) {
        // if the endIdx supplied is larger than the length of our string, 
        // we'll use the length instead 
        res += this[i]; 
    }
    return res; 
}
```


### Array.prototype.dups ###

```js
Array.prototype.dups = function() {
    let h = {}; 
    // builds up a hash of key-values, where values are all indices a particular key exists 
    for (let i = 0; i < this.length; i++) {
        if (h[this[i]] === undefined) {
            h[this[i]] = [i]; 
        } else {
            h[this[i]].push(i); 
        }
    }
    // we delete all key-value pairs where the number of indices < 2 
    for (let k in h) {
        if (h[k].length < 2) delete h[k]; 
    }
    return h; 
}

```

### Array.prototype.twoSum ###

```js
// O(n^2) time complexity
Array.prototype.twoSum = function() {
    let res = []; 
    for (let i = 0; i < this.length-1; i++) {
        for (let j = i+1; j < this.length; j++) {
            // starting the inner index 'j' at 'i+1' means that 
            // all of our pairs [i, j] are ordered 
            if (this[i] + this[j] === 0) {
                res.push([i, j]); 
            }
        }
    }
    return res; 
}
```


### Array.prototype.myJoin ###

```js
// set the default separator to an empty string
Array.prototype.myJoin = function(separator='') {
    let res = ""; 
    let arr = this.map(x => x.toString()); 
    for (let i = 0; i < arr.length; i++) {
        res += arr[i]; 
        res += separator; 
    }
    // chop off last character, e.g. if the separator is "+", 
    // we get "1+2+3" instead of "1+2+3+"
    return separator === '' ? res : res.slice(0, res.length-1); 
}
```

### Doubler ###
```js
function doubler(arr) {
    return arr.map(x => 2*x); 
}
```

### myBind ###
```js
Function.prototype.myBind = function(context) {
    // arguments is Array-like (its enumerable, and we can get the length, etc.), but it isn't
    // an Array instance, so we can't use `Array.prototype.slice()` on it, so we convert arguments
    // into an array instance with the `Array.from()` method 
    
    // we don't want to include context in our bindArgs
    let bindArgs = Array.from(arguments).slice(1); 
    
    // we save context(the meaning of 'this') into variable "that" in our outer scope! 
    // since once we go into the inner function, the "this" gets reassigned 
    let that = this; 
    return function() {
        let callArgs = Array.from(arguments);
        
        // remember that apply takes an array as a second argument 
        return that.apply(context, bindArgs.concat(callArgs)); 
    }
}
```


### myCurry ###
```js
Function.prototype.myCurry = function(numArgs) {
    let res = []; 
    let that = this; 
    return function _curry(arg) {
        res.push(arg); 
        if (res.length === numArgs) {
            // context does not matter, hence we put 'null' as the first argument  
            return that.apply(null, res);  
        } else {
            // we must return the function uninvoked (to be potentially called on later) 
            return _curry; 
        }
    }
}
```

### Function.prototype.myApply ###
```js
Function.prototype.myApply = function(context, argsArr = []) {
    // set a default argument to an empty array 
    // remember argsArr (unlike in myCall) is a single argument
    // which we will destructure using the spread operator
    return this.bind(context)(...argsArr); 
}
```


### Function.prototype.myCall ###
```js
Function.prototype.myCall = function (context, ...argsArr) {
    // the spread operator collects all arguments aside from the first into an array 
    // we use the spread operator again to separate the array into comma separated arguments 
    return this.bind(context)(...argsArr);
}
```

### Array.prototype.inherits ###

```js
Function.prototype.inherits = function(ParentClass) {
    function Surrogate() {}; 
    Surrogate.prototype = ParentClass.prototype; 
    this.prototype = new Surrogate(); 
    this.prototype.constructor = this; 
}
```


## Enumerables
Note: **For each** of these problems remember that `.myEach` (like the native `.forEach`) DOES NOT RETURN. 

### Array.prototype.myEach ###
```js
Array.prototype.myEach = function(callback) {
    for (let i = 0; i < this.length; i++) {
        // note that we don't need to explicitly return here 
        callback(this[i]); 
    }
}
```

### Array.prototype.myFilter ###
```js
Array.prototype.myFilter = function(callback) {
    let res = []; 
    this.myEach(el => {
        if (callback(el)) {
            res.push(el); 
        }
    }); 
    return res; 
}

```

### Array.prototype.mySome ###
```js
Array.prototype.mySome = function(callback) {
    let res = []; 
    this.myEach(el => {
        if (callback(el)) {
            res.push(el); 
        }
    })
    return res.length > 0 ? true : false; 
}
```

### Array.prototype.myEvery ###
```js
Array.prototype.myEvery = function(callback) {
    let res = []; 
    this.myEach(el => {
        if (callback(el)) {
            res.push(el); 
        }
    })
    return res.length === this.length ? true : false; 
}
```

### Array.prototype.myReduce ###
```js
Array.prototype.myReduce = function(callback, acc=this.shift()) {
    this.myEach(el => {
        acc = callback(acc, el); 
    })
    return acc; 
}
```

### Array.prototype.myReject ###
```js
Array.prototype.myReject = function(callback) {
    let res = []; 
    this.myEach(el => {
        if (callback(el) === false) {
            res.push(el); 
        }
    })
    return res; 
}
````


