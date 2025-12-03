# מדריך למתחילים: Higher-Order Functions (HOF) ב-JavaScript

## תוכן עניינים
1. [מבוא](#מבוא)
2. [מה זה Higher-Order Function?](#מה-זה-higher-order-function)
3. [Array Methods - HOF המובנים](#array-methods---hof-המובנים)
4. [יצירת HOF משלנו](#יצירת-hof-משלנו)
5. [Currying](#currying)
6. [Function Composition](#function-composition)
7. [Decorators](#decorators)
8. [דוגמאות מעשיות](#דוגמאות-מעשיות)
9. [Best Practices](#best-practices)

## מבוא

Higher-Order Functions הם אחד המושגים החשובים ביותר ב-JavaScript מודרני. הם מאפשרים לנו לכתוב קוד יותר אלגנטי, קריא ומודולרי.

### למה זה חשוב?
- 🎯 **קוד קצר וקריא יותר**
- ♻️ **שימוש חוזר בקוד**
- 🧩 **מודולריות**
- 🔧 **גמישות**
- ✨ **Functional Programming**

## מה זה Higher-Order Function?

**Higher-Order Function** היא פונקציה שעושה לפחות אחד מהדברים הבאים:
1. **מקבלת פונקציה כפרמטר** (callback)
2. **מחזירה פונקציה חדשה**

### דוגמה פשוטה:

```javascript
// 1. פונקציה שמקבלת פונקציה
function repeat(n, action) {
    for (let i = 0; i < n; i++) {
        action(i);
    }
}

repeat(3, console.log);
// 0
// 1
// 2

repeat(3, (i) => console.log(`Hello ${i}`));
// Hello 0
// Hello 1
// Hello 2

// 2. פונקציה שמחזירה פונקציה
function multiplyBy(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### למה זה שימושי?

```javascript
// ❌ בלי HOF - קוד חוזר
const numbers = [1, 2, 3, 4, 5];

const doubled = [];
for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}

const tripled = [];
for (let i = 0; i < numbers.length; i++) {
    tripled.push(numbers[i] * 3);
}

// ✅ עם HOF - קוד נקי וקריא
const doubled = numbers.map(n => n * 2);
const tripled = numbers.map(n => n * 3);
```

## Array Methods - HOF המובנים

JavaScript מספקת HOF מובנים רבים לעבודה עם מערכים. אלו הם הכלים החשובים ביותר!

### 1. map() - המרה

**מטרה**: יוצרת מערך חדש על ידי החלת פונקציה על כל איבר.

```javascript
const numbers = [1, 2, 3, 4, 5];

// דוגמה 1: כפל ב-2
const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// דוגמה 2: המרה לאובייקטים
const users = ['Alice', 'Bob', 'Charlie'];
const userObjects = users.map(name => ({
    id: Math.random(),
    name: name,
    email: `${name.toLowerCase()}@example.com`
}));
console.log(userObjects);
// [
//   { id: 0.123..., name: 'Alice', email: 'alice@example.com' },
//   { id: 0.456..., name: 'Bob', email: 'bob@example.com' },
//   { id: 0.789..., name: 'Charlie', email: 'charlie@example.com' }
// ]

// דוגמה 3: עם index
const withIndex = numbers.map((num, index) => `${index}: ${num}`);
console.log(withIndex); // ['0: 1', '1: 2', '2: 3', '3: 4', '4: 5']

// דוגמה 4: שליפת property מאובייקט
const products = [
    { id: 1, name: 'Laptop', price: 1000 },
    { id: 2, name: 'Phone', price: 500 },
    { id: 3, name: 'Tablet', price: 300 }
];

const prices = products.map(product => product.price);
console.log(prices); // [1000, 500, 300]

const names = products.map(p => p.name);
console.log(names); // ['Laptop', 'Phone', 'Tablet']
```

**💡 טיפ חשוב**: `map()` תמיד מחזירה מערך באותו אורך של המערך המקורי!

### 2. filter() - סינון

**מטרה**: יוצרת מערך חדש עם רק האיברים שעוברים את התנאי.

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// דוגמה 1: מספרים זוגיים
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens); // [2, 4, 6, 8, 10]

// דוגמה 2: מספרים גדולים מ-5
const greaterThanFive = numbers.filter(n => n > 5);
console.log(greaterThanFive); // [6, 7, 8, 9, 10]

// דוגמה 3: סינון אובייקטים
const users = [
    { name: 'Alice', age: 25, active: true },
    { name: 'Bob', age: 17, active: false },
    { name: 'Charlie', age: 30, active: true },
    { name: 'David', age: 16, active: true }
];

const adults = users.filter(user => user.age >= 18);
console.log(adults);
// [{ name: 'Alice', age: 25, active: true }, { name: 'Charlie', age: 30, active: true }]

const activeUsers = users.filter(user => user.active);
console.log(activeUsers.map(u => u.name)); // ['Alice', 'Charlie', 'David']

// דוגמה 4: סינון מורכב
const activeAdults = users.filter(user => user.age >= 18 && user.active);
console.log(activeAdults.map(u => u.name)); // ['Alice', 'Charlie']

// דוגמה 5: הסרת ערכים ריקים
const mixed = [0, 1, false, 2, '', 3, null, undefined, 4, NaN];
const truthy = mixed.filter(Boolean);
console.log(truthy); // [1, 2, 3, 4]

// דוגמה 6: הסרת duplicates
const withDuplicates = [1, 2, 2, 3, 3, 3, 4, 5, 5];
const unique = withDuplicates.filter((item, index, arr) => {
    return arr.indexOf(item) === index;
});
console.log(unique); // [1, 2, 3, 4, 5]
```

### 3. reduce() - צמצום/צבירה

**מטרה**: "מצמצמת" מערך לערך אחד על ידי צבירה.

```javascript
const numbers = [1, 2, 3, 4, 5];

// דוגמה 1: סכום
const sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum); // 15

// דוגמה 2: מכפלה
const product = numbers.reduce((total, num) => total * num, 1);
console.log(product); // 120

// דוגמה 3: מציאת המקסימום
const max = numbers.reduce((max, num) => num > max ? num : max);
console.log(max); // 5

// דוגמה 4: ספירת פריטים
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const count = fruits.reduce((acc, fruit) => {
    acc[fruit] = (acc[fruit] || 0) + 1;
    return acc;
}, {});
console.log(count); // { apple: 3, banana: 2, orange: 1 }

// דוגמה 5: שיטוח מערך מקונן (flatten)
const nested = [[1, 2], [3, 4], [5, 6]];
const flattened = nested.reduce((acc, arr) => acc.concat(arr), []);
console.log(flattened); // [1, 2, 3, 4, 5, 6]

// דוגמה 6: קיבוץ לפי property
const people = [
    { name: 'Alice', department: 'IT' },
    { name: 'Bob', department: 'HR' },
    { name: 'Charlie', department: 'IT' },
    { name: 'David', department: 'Sales' }
];

const groupedByDept = people.reduce((acc, person) => {
    const dept = person.department;
    if (!acc[dept]) {
        acc[dept] = [];
    }
    acc[dept].push(person.name);
    return acc;
}, {});
console.log(groupedByDept);
// {
//   IT: ['Alice', 'Charlie'],
//   HR: ['Bob'],
//   Sales: ['David']
// }

// דוגמה 7: חישוב ממוצע
const grades = [85, 90, 78, 92, 88];
const average = grades.reduce((sum, grade, _, arr) => {
    sum += grade;
    if (_ === arr.length - 1) {
        return sum / arr.length;
    }
    return sum;
}, 0);
console.log(average); // 86.6

// דוגמה 8: בניית אובייקט מערכים
const keys = ['name', 'age', 'city'];
const values = ['John', 30, 'New York'];
const obj = keys.reduce((acc, key, index) => {
    acc[key] = values[index];
    return acc;
}, {});
console.log(obj); // { name: 'John', age: 30, city: 'New York' }
```

**💡 הבנת reduce**:
- **accumulator (acc)**: הערך המצטבר
- **current value**: האיבר הנוכחי
- **initial value**: הערך ההתחלתי (פרמטר שני)

### 4. forEach() - לולאה

**מטרה**: מבצעת פעולה על כל איבר (לא מחזירה מערך חדש).

```javascript
const numbers = [1, 2, 3, 4, 5];

// דוגמה 1: הדפסה
numbers.forEach(num => console.log(num));

// דוגמה 2: עם index
numbers.forEach((num, index) => {
    console.log(`Index ${index}: ${num}`);
});

// דוגמה 3: שינוי מערך אחר
const doubled = [];
numbers.forEach(num => doubled.push(num * 2));
console.log(doubled); // [2, 4, 6, 8, 10]

// דוגמה 4: side effects
const users = [
    { name: 'Alice', email: 'alice@example.com' },
    { name: 'Bob', email: 'bob@example.com' }
];

users.forEach(user => {
    // שליחת אימייל (דמה)
    console.log(`Sending email to ${user.email}`);
});
```

**⚠️ הבדל חשוב**: `forEach` לא מחזירה ערך, בניגוד ל-`map`!

```javascript
// ❌ לא יעבוד כמו שאתה מצפה
const doubled = numbers.forEach(n => n * 2);
console.log(doubled); // undefined

// ✅ השתמש ב-map במקום
const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10]
```

### 5. find() - מציאת איבר

**מטרה**: מחזירה את האיבר הראשון שעומד בתנאי.

```javascript
const users = [
    { id: 1, name: 'Alice', age: 25 },
    { id: 2, name: 'Bob', age: 30 },
    { id: 3, name: 'Charlie', age: 35 }
];

// דוגמה 1: מציאה לפי ID
const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: 'Bob', age: 30 }

// דוגמה 2: מציאה לפי תנאי
const adult = users.find(u => u.age >= 30);
console.log(adult); // { id: 2, name: 'Bob', age: 30 } (הראשון!)

// דוגמה 3: אם לא נמצא
const young = users.find(u => u.age < 20);
console.log(young); // undefined

// דוגמה 4: במספרים
const numbers = [5, 12, 8, 130, 44];
const found = numbers.find(num => num > 10);
console.log(found); // 12 (הראשון שגדול מ-10)
```

### 6. findIndex() - מציאת אינדקס

**מטרה**: מחזירה את האינדקס של האיבר הראשון שעומד בתנאי.

```javascript
const users = [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' },
    { id: 3, name: 'Charlie' }
];

const index = users.findIndex(u => u.id === 2);
console.log(index); // 1

const notFound = users.findIndex(u => u.id === 999);
console.log(notFound); // -1
```

### 7. some() - בדיקה אם יש

**מטרה**: בודקת אם **לפחות איבר אחד** עומד בתנאי.

```javascript
const numbers = [1, 2, 3, 4, 5];

// דוגמה 1: האם יש זוגיים?
const hasEven = numbers.some(n => n % 2 === 0);
console.log(hasEven); // true

// דוגמה 2: האם יש גדולים מ-10?
const hasLarge = numbers.some(n => n > 10);
console.log(hasLarge); // false

// דוגמה 3: עם אובייקטים
const users = [
    { name: 'Alice', age: 17 },
    { name: 'Bob', age: 25 }
];

const hasAdult = users.some(user => user.age >= 18);
console.log(hasAdult); // true

// דוגמה 4: בדיקת קיום
const emails = ['alice@example.com', 'bob@example.com'];
const hasGmail = emails.some(email => email.includes('@gmail.com'));
console.log(hasGmail); // false
```

### 8. every() - בדיקה אם כולם

**מטרה**: בודקת אם **כל האיברים** עומדים בתנאי.

```javascript
const numbers = [2, 4, 6, 8, 10];

// דוגמה 1: האם כולם זוגיים?
const allEven = numbers.every(n => n % 2 === 0);
console.log(allEven); // true

// דוגמה 2: האם כולם חיוביים?
const allPositive = numbers.every(n => n > 0);
console.log(allPositive); // true

// דוגמה 3: עם אובייקטים
const users = [
    { name: 'Alice', age: 25 },
    { name: 'Bob', age: 30 },
    { name: 'Charlie', age: 35 }
];

const allAdults = users.every(user => user.age >= 18);
console.log(allAdults); // true

// דוגמה 4: בדיקת תקינות טופס
const formFields = [
    { name: 'email', value: 'test@test.com', valid: true },
    { name: 'password', value: '12345', valid: true },
    { name: 'age', value: '25', valid: true }
];

const isFormValid = formFields.every(field => field.valid);
console.log(isFormValid); // true
```

### 9. sort() - מיון

**מטרה**: ממיינת מערך (משנה את המערך המקורי!).

```javascript
// ⚠️ זהירות! sort() משנה את המערך המקורי

// דוגמה 1: מיון מספרים (נכון!)
const numbers = [3, 1, 4, 1, 5, 9, 2, 6];
numbers.sort((a, b) => a - b); // עולה
console.log(numbers); // [1, 1, 2, 3, 4, 5, 6, 9]

numbers.sort((a, b) => b - a); // יורד
console.log(numbers); // [9, 6, 5, 4, 3, 2, 1, 1]

// דוגמה 2: מיון strings
const fruits = ['banana', 'apple', 'cherry', 'date'];
fruits.sort();
console.log(fruits); // ['apple', 'banana', 'cherry', 'date']

// דוגמה 3: מיון אובייקטים לפי property
const users = [
    { name: 'Charlie', age: 35 },
    { name: 'Alice', age: 25 },
    { name: 'Bob', age: 30 }
];

// לפי שם
users.sort((a, b) => a.name.localeCompare(b.name));
console.log(users);
// [
//   { name: 'Alice', age: 25 },
//   { name: 'Bob', age: 30 },
//   { name: 'Charlie', age: 35 }
// ]

// לפי גיל
users.sort((a, b) => a.age - b.age);
console.log(users);

// דוגמה 4: מיון ללא שינוי המקור (עם spread)
const original = [3, 1, 4, 1, 5];
const sorted = [...original].sort((a, b) => a - b);
console.log(original); // [3, 1, 4, 1, 5] (לא השתנה!)
console.log(sorted); // [1, 1, 3, 4, 5]
```

### 10. שילוב של Array Methods - Chaining

זו הכוח האמיתי של HOF! 🔥

```javascript
const products = [
    { id: 1, name: 'Laptop', price: 1000, category: 'Electronics', inStock: true },
    { id: 2, name: 'Phone', price: 500, category: 'Electronics', inStock: true },
    { id: 3, name: 'Shirt', price: 30, category: 'Clothing', inStock: false },
    { id: 4, name: 'Tablet', price: 300, category: 'Electronics', inStock: true },
    { id: 5, name: 'Shoes', price: 80, category: 'Clothing', inStock: true }
];

// דוגמה 1: מוצרי אלקטרוניקה במלאי, ממוינים לפי מחיר
const availableElectronics = products
    .filter(p => p.category === 'Electronics')
    .filter(p => p.inStock)
    .sort((a, b) => a.price - b.price)
    .map(p => p.name);

console.log(availableElectronics); // ['Tablet', 'Phone', 'Laptop']

// דוגמה 2: סכום מחירי המוצרים במלאי
const totalValue = products
    .filter(p => p.inStock)
    .reduce((sum, p) => sum + p.price, 0);

console.log(totalValue); // 1880

// דוגמה 3: מחירים מוזלים של מוצרי ביגוד
const clothingDiscounts = products
    .filter(p => p.category === 'Clothing')
    .map(p => ({
        name: p.name,
        originalPrice: p.price,
        discountedPrice: p.price * 0.8
    }));

console.log(clothingDiscounts);

// דוגמה 4: ממוצע מחירים לפי קטגוריה
const avgByCategory = products.reduce((acc, product) => {
    const cat = product.category;
    if (!acc[cat]) {
        acc[cat] = { sum: 0, count: 0 };
    }
    acc[cat].sum += product.price;
    acc[cat].count += 1;
    return acc;
}, {});

Object.keys(avgByCategory).forEach(cat => {
    const avg = avgByCategory[cat].sum / avgByCategory[cat].count;
    console.log(`${cat}: $${avg}`);
});
// Electronics: $600
// Clothing: $55

// דוגמה 5: שאילתה מורכבת
const expensiveInStockItems = products
    .filter(p => p.inStock)
    .filter(p => p.price > 100)
    .sort((a, b) => b.price - a.price)
    .map(p => `${p.name} - $${p.price}`);

console.log(expensiveInStockItems);
// ['Laptop - $1000', 'Phone - $500', 'Tablet - $300']
```

## יצירת HOF משלנו

עכשיו ניצור HOF מותאמים אישית!

### פונקציות שמקבלות פונקציות

```javascript
// דוגמה 1: פונקציה שחוזרת על פעולה
function repeat(n, action) {
    for (let i = 0; i < n; i++) {
        action(i);
    }
}

repeat(3, i => console.log(`Hello ${i}`));
// Hello 0
// Hello 1
// Hello 2

// דוגמה 2: פונקציה שמבצעת פעולה עם delay
function delayed(fn, ms) {
    setTimeout(fn, ms);
}

delayed(() => console.log("This is delayed!"), 2000);

// דוגמה 3: מדידת זמן ריצה
function measureTime(fn) {
    const start = Date.now();
    fn();
    const end = Date.now();
    console.log(`Execution time: ${end - start}ms`);
}

measureTime(() => {
    let sum = 0;
    for (let i = 0; i < 1000000; i++) {
        sum += i;
    }
});

// דוגמה 4: retry logic
function retry(fn, maxAttempts = 3) {
    for (let i = 0; i < maxAttempts; i++) {
        try {
            return fn();
        } catch (error) {
            if (i === maxAttempts - 1) throw error;
            console.log(`Attempt ${i + 1} failed, retrying...`);
        }
    }
}

// דוגמה 5: once - פונקציה שרצה רק פעם אחת
function once(fn) {
    let hasRun = false;
    let result;
    
    return function(...args) {
        if (!hasRun) {
            hasRun = true;
            result = fn(...args);
        }
        return result;
    };
}

const initializeApp = once(() => {
    console.log("App initialized!");
    return { status: 'ready' };
});

console.log(initializeApp()); // App initialized! { status: 'ready' }
console.log(initializeApp()); // { status: 'ready' } (לא מדפיס שוב!)

// דוגמה 6: map משלנו
function myMap(array, transform) {
    const result = [];
    for (const item of array) {
        result.push(transform(item));
    }
    return result;
}

console.log(myMap([1, 2, 3], x => x * 2)); // [2, 4, 6]

// דוגמה 7: filter משלנו
function myFilter(array, predicate) {
    const result = [];
    for (const item of array) {
        if (predicate(item)) {
            result.push(item);
        }
    }
    return result;
}

console.log(myFilter([1, 2, 3, 4, 5], x => x % 2 === 0)); // [2, 4]

// דוגמה 8: reduce משלנו
function myReduce(array, reducer, initialValue) {
    let accumulator = initialValue;
    for (const item of array) {
        accumulator = reducer(accumulator, item);
    }
    return accumulator;
}

console.log(myReduce([1, 2, 3, 4], (sum, n) => sum + n, 0)); // 10
```

### פונקציות שמחזירות פונקציות

```javascript
// דוגמה 1: פונקציה שיוצרת multiplier
function createMultiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);
const quadruple = createMultiplier(4);

console.log(double(5)); // 10
console.log(triple(5)); // 15
console.log(quadruple(5)); // 20

// דוגמה 2: פונקציה שיוצרת greeter
function createGreeter(greeting) {
    return function(name) {
        return `${greeting}, ${name}!`;
    };
}

const sayHello = createGreeter("Hello");
const sayHi = createGreeter("Hi");
const shalom = createGreeter("Shalom");

console.log(sayHello("Alice")); // Hello, Alice!
console.log(sayHi("Bob")); // Hi, Bob!
console.log(shalom("Charlie")); // Shalom, Charlie!

// דוגמה 3: פונקציה ליצירת validators
function createValidator(regex, errorMessage) {
    return function(value) {
        if (!regex.test(value)) {
            return { valid: false, error: errorMessage };
        }
        return { valid: true };
    };
}

const validateEmail = createValidator(
    /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
    "Invalid email format"
);

const validatePhone = createValidator(
    /^\d{10}$/,
    "Phone must be 10 digits"
);

console.log(validateEmail("test@example.com")); // { valid: true }
console.log(validateEmail("invalid")); // { valid: false, error: 'Invalid email format' }
console.log(validatePhone("1234567890")); // { valid: true }

// דוגמה 4: פונקציה ליצירת range
function range(start, end) {
    return function(step = 1) {
        const result = [];
        for (let i = start; i <= end; i += step) {
            result.push(i);
        }
        return result;
    };
}

const oneToTen = range(1, 10);
console.log(oneToTen()); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log(oneToTen(2)); // [1, 3, 5, 7, 9]

// דוגמה 5: מחשבון עם state
function createCalculator(initial = 0) {
    let value = initial;
    
    return {
        add: function(n) {
            value += n;
            return this;
        },
        subtract: function(n) {
            value -= n;
            return this;
        },
        multiply: function(n) {
            value *= n;
            return this;
        },
        divide: function(n) {
            value /= n;
            return this;
        },
        getValue: function() {
            return value;
        }
    };
}

const calc = createCalculator(10);
const result = calc
    .add(5)
    .multiply(2)
    .subtract(10)
    .getValue();

console.log(result); // 20
```

## Currying

Currying זו טכניקה שהופכת פונקציה עם מספר פרמטרים לשרשרת של פונקציות שמקבלות פרמטר אחד.

### מה זה Currying?

```javascript
// פונקציה רגילה
function add(a, b, c) {
    return a + b + c;
}
console.log(add(1, 2, 3)); // 6

// אותה פונקציה עם Currying
function curriedAdd(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        };
    };
}
console.log(curriedAdd(1)(2)(3)); // 6

// עם arrow functions (יותר קצר!)
const curriedAdd2 = a => b => c => a + b + c;
console.log(curriedAdd2(1)(2)(3)); // 6
```

### למה Currying שימושי?

```javascript
// דוגמה 1: יצירת פונקציות מותאמות אישית
const add = a => b => a + b;

const add5 = add(5);
const add10 = add(10);

console.log(add5(3)); // 8
console.log(add10(3)); // 13

// דוגמה 2: discount calculator
const discount = percentage => price => price * (1 - percentage / 100);

const tenPercentOff = discount(10);
const twentyPercentOff = discount(20);
const fiftyPercentOff = discount(50);

console.log(tenPercentOff(100)); // 90
console.log(twentyPercentOff(100)); // 80
console.log(fiftyPercentOff(100)); // 50

// דוגמה 3: log levels
const log = level => message => {
    console.log(`[${level}] ${new Date().toISOString()}: ${message}`);
};

const info = log('INFO');
const warning = log('WARNING');
const error = log('ERROR');

info('Application started');
warning('Low memory');
error('Connection failed');

// דוגמה 4: API request builder
const apiRequest = method => url => data => {
    return {
        method,
        url,
        data,
        headers: { 'Content-Type': 'application/json' }
    };
};

const get = apiRequest('GET');
const post = apiRequest('POST');
const put = apiRequest('PUT');

const getUsers = get('/api/users');
const postUser = post('/api/users');

console.log(getUsers(null));
console.log(postUser({ name: 'John' }));

// דוגמה 5: מסנן גמיש
const filterBy = property => value => array => {
    return array.filter(item => item[property] === value);
};

const users = [
    { name: 'Alice', role: 'admin', active: true },
    { name: 'Bob', role: 'user', active: true },
    { name: 'Charlie', role: 'admin', active: false }
];

const filterByRole = filterBy('role');
const filterAdmins = filterByRole('admin');

console.log(filterAdmins(users));
// [{ name: 'Alice', ... }, { name: 'Charlie', ... }]

// דוגמה 6: Curry helper function
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...args2) {
                return curried.apply(this, args.concat(args2));
            };
        }
    };
}

// שימוש ב-curry helper
function multiply(a, b, c) {
    return a * b * c;
}

const curriedMultiply = curry(multiply);

console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24
console.log(curriedMultiply(2)(3, 4)); // 24
console.log(curriedMultiply(2, 3, 4)); // 24
```

## Function Composition

שילוב של פונקציות לפונקציה אחת מורכבת.

### מה זה Composition?

```javascript
// במקום:
const result = func3(func2(func1(data)));

// נשתמש ב-composition:
const composed = compose(func3, func2, func1);
const result = composed(data);
```

### דוגמאות:

```javascript
// דוגמה 1: compose פשוט
function compose(f, g) {
    return function(x) {
        return f(g(x));
    };
}

const double = x => x * 2;
const addOne = x => x + 1;

const doubleThenAddOne = compose(addOne, double);
console.log(doubleThenAddOne(5)); // 11 (5 * 2 + 1)

// דוגמה 2: compose עם מספר פונקציות
function composeMany(...fns) {
    return function(x) {
        return fns.reduceRight((acc, fn) => fn(acc), x);
    };
}

const multiplyBy3 = x => x * 3;
const subtract2 = x => x - 2;
const square = x => x * x;

const composed = composeMany(square, subtract2, multiplyBy3);
console.log(composed(5)); // 169
// 5 * 3 = 15
// 15 - 2 = 13
// 13 * 13 = 169

// דוגמה 3: pipe (כמו compose אבל הפוך)
function pipe(...fns) {
    return function(x) {
        return fns.reduce((acc, fn) => fn(acc), x);
    };
}

const piped = pipe(multiplyBy3, subtract2, square);
console.log(piped(5)); // 169 (אותו תוצאה!)

// דוגמה 4: עיבוד טקסט
const trim = str => str.trim();
const toLowerCase = str => str.toLowerCase();
const removeSpaces = str => str.replace(/\s+/g, '-');

const slugify = pipe(
    trim,
    toLowerCase,
    removeSpaces
);

console.log(slugify("  Hello World  ")); // "hello-world"

// דוגמה 5: עיבוד מספרים
const isEven = n => n % 2 === 0;
const double2 = n => n * 2;
const increment = n => n + 1;

const processNumber = pipe(
    increment,
    double2
);

const numbers = [1, 2, 3, 4, 5];
const processed = numbers.map(processNumber);
console.log(processed); // [4, 6, 8, 10, 12]

// דוגמה 6: data transformation
const parseJSON = str => JSON.parse(str);
const extractNames = arr => arr.map(user => user.name);
const sortAlphabetically = arr => [...arr].sort();

const processUsers = pipe(
    parseJSON,
    extractNames,
    sortAlphabetically
);

const jsonData = '[{"name":"Charlie"},{"name":"Alice"},{"name":"Bob"}]';
console.log(processUsers(jsonData)); // ["Alice", "Bob", "Charlie"]

// דוגמה 7: validation pipeline
const notEmpty = value => value.length > 0;
const isString = value => typeof value === 'string';
const maxLength = max => value => value.length <= max;

function validate(...validators) {
    return function(value) {
        return validators.every(validator => validator(value));
    };
}

const validateUsername = validate(
    isString,
    notEmpty,
    maxLength(20)
);

console.log(validateUsername("john_doe")); // true
console.log(validateUsername("")); // false
console.log(validateUsername("this_username_is_way_too_long")); // false
```

## Decorators

פונקציות שמרחיבות או משנות את התנהגות הפונקציה המקורית.

```javascript
// דוגמה 1: logging decorator
function withLogging(fn) {
    return function(...args) {
        console.log(`Calling ${fn.name} with args:`, args);
        const result = fn(...args);
        console.log(`Result:`, result);
        return result;
    };
}

function add(a, b) {
    return a + b;
}

const addWithLogging = withLogging(add);
addWithLogging(2, 3);
// Calling add with args: [2, 3]
// Result: 5

// דוגמה 2: timing decorator
function withTiming(fn) {
    return function(...args) {
        const start = performance.now();
        const result = fn(...args);
        const end = performance.now();
        console.log(`${fn.name} took ${(end - start).toFixed(2)}ms`);
        return result;
    };
}

function slowFunction() {
    let sum = 0;
    for (let i = 0; i < 1000000; i++) {
        sum += i;
    }
    return sum;
}

const timedFunction = withTiming(slowFunction);
timedFunction();

// דוגמה 3: memoization decorator
function memoize(fn) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            console.log('Returning from cache');
            return cache.get(key);
        }
        
        console.log('Computing result');
        const result = fn(...args);
        cache.set(key, result);
        return result;
    };
}

function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

const memoizedFib = memoize(fibonacci);
console.log(memoizedFib(10)); // Computing result
console.log(memoizedFib(10)); // Returning from cache

// דוגמה 4: retry decorator
function withRetry(fn, maxAttempts = 3) {
    return function(...args) {
        for (let i = 0; i < maxAttempts; i++) {
            try {
                return fn(...args);
            } catch (error) {
                if (i === maxAttempts - 1) throw error;
                console.log(`Attempt ${i + 1} failed, retrying...`);
            }
        }
    };
}

// דוגמה 5: validation decorator
function withValidation(fn, validator) {
    return function(...args) {
        if (!validator(...args)) {
            throw new Error('Invalid arguments');
        }
        return fn(...args);
    };
}

const divide = (a, b) => a / b;
const safeDivide = withValidation(
    divide,
    (a, b) => b !== 0
);

console.log(safeDivide(10, 2)); // 5
// console.log(safeDivide(10, 0)); // Error: Invalid arguments

// דוגמה 6: שילוב decorators
function compose(...decorators) {
    return function(fn) {
        return decorators.reduceRight((decorated, decorator) => {
            return decorator(decorated);
        }, fn);
    };
}

const enhancedAdd = compose(
    withLogging,
    withTiming
)(add);

enhancedAdd(5, 7);
```

## דוגמאות מעשיות

### 1. מערכת סינון מתקדמת

```javascript
const products = [
    { id: 1, name: 'Laptop', price: 1000, category: 'Electronics', rating: 4.5, inStock: true },
    { id: 2, name: 'Phone', price: 500, category: 'Electronics', rating: 4.7, inStock: true },
    { id: 3, name: 'Shirt', price: 30, category: 'Clothing', rating: 4.0, inStock: false },
    { id: 4, name: 'Tablet', price: 300, category: 'Electronics', rating: 4.3, inStock: true },
    { id: 5, name: 'Shoes', price: 80, category: 'Clothing', rating: 4.6, inStock: true },
    { id: 6, name: 'Watch', price: 200, category: 'Accessories', rating: 4.8, inStock: true }
];

// פונקציות סינון
const byCategory = category => product => product.category === category;
const byPriceRange = (min, max) => product => product.price >= min && product.price <= max;
const byRating = minRating => product => product.rating >= minRating;
const inStock = product => product.inStock;

// פונקציה מרכזית לסינון
function filterProducts(products, ...filters) {
    return products.filter(product => 
        filters.every(filter => filter(product))
    );
}

// שימוש
const result = filterProducts(
    products,
    byCategory('Electronics'),
    byPriceRange(200, 600),
    byRating(4.5),
    inStock
);

console.log(result);
// [{ id: 2, name: 'Phone', ... }]

// חיפוש גמיש
function search(products) {
    return {
        inCategory: function(category) {
            this.filters = this.filters || [];
            this.filters.push(byCategory(category));
            return this;
        },
        priceRange: function(min, max) {
            this.filters = this.filters || [];
            this.filters.push(byPriceRange(min, max));
            return this;
        },
        minRating: function(rating) {
            this.filters = this.filters || [];
            this.filters.push(byRating(rating));
            return this;
        },
        onlyInStock: function() {
            this.filters = this.filters || [];
            this.filters.push(inStock);
            return this;
        },
        execute: function() {
            return filterProducts(products, ...this.filters);
        }
    };
}

// שימוש fluent
const results = search(products)
    .inCategory('Electronics')
    .priceRange(200, 600)
    .minRating(4.5)
    .onlyInStock()
    .execute();

console.log(results);
```

### 2. Data Pipeline

```javascript
const students = [
    { name: 'Alice', grades: [85, 90, 78, 92, 88], attendance: 95 },
    { name: 'Bob', grades: [70, 65, 72, 68, 71], attendance: 80 },
    { name: 'Charlie', grades: [95, 98, 92, 96, 94], attendance: 98 },
    { name: 'David', grades: [60, 58, 62, 59, 61], attendance: 70 },
    { name: 'Eve', grades: [88, 85, 90, 87, 89], attendance: 92 }
];

// פונקציות עיבוד
const calculateAverage = grades => 
    grades.reduce((sum, grade) => sum + grade, 0) / grades.length;

const addAverageGrade = student => ({
    ...student,
    average: calculateAverage(student.grades)
});

const addStatus = student => ({
    ...student,
    status: student.average >= 70 && student.attendance >= 75 ? 'Pass' : 'Fail'
});

const addHonors = student => ({
    ...student,
    honors: student.average >= 90 && student.attendance >= 90
});

// Pipeline
const processStudents = pipe(
    students => students.map(addAverageGrade),
    students => students.map(addStatus),
    students => students.map(addHonors),
    students => students.sort((a, b) => b.average - a.average)
);

const processedStudents = processStudents(students);
console.log(processedStudents);

// קבלת סטטיסטיקות
const stats = {
    total: processedStudents.length,
    passed: processedStudents.filter(s => s.status === 'Pass').length,
    failed: processedStudents.filter(s => s.status === 'Fail').length,
    honors: processedStudents.filter(s => s.honors).length,
    averageGrade: calculateAverage(processedStudents.map(s => s.average)),
    topStudent: processedStudents[0].name
};

console.log(stats);
```

### 3. Event Handling System

```javascript
function createEventEmitter() {
    const listeners = new Map();
    
    return {
        on: function(event, handler) {
            if (!listeners.has(event)) {
                listeners.set(event, []);
            }
            listeners.get(event).push(handler);
            return this;
        },
        
        off: function(event, handler) {
            if (listeners.has(event)) {
                const handlers = listeners.get(event);
                const index = handlers.indexOf(handler);
                if (index > -1) {
                    handlers.splice(index, 1);
                }
            }
            return this;
        },
        
        emit: function(event, data) {
            if (listeners.has(event)) {
                listeners.get(event).forEach(handler => handler(data));
            }
            return this;
        },
        
        once: function(event, handler) {
            const onceHandler = (data) => {
                handler(data);
                this.off(event, onceHandler);
            };
            return this.on(event, onceHandler);
        }
    };
}

// שימוש
const emitter = createEventEmitter();

emitter
    .on('login', user => console.log(`User ${user.name} logged in`))
    .on('login', user => console.log(`Sending welcome email to ${user.email}`))
    .once('login', user => console.log('First login bonus!'));

emitter.emit('login', { name: 'Alice', email: 'alice@example.com' });
// User Alice logged in
// Sending welcome email to alice@example.com
// First login bonus!

emitter.emit('login', { name: 'Bob', email: 'bob@example.com' });
// User Bob logged in
// Sending welcome email to bob@example.com
// (לא מדפיס First login bonus!)
```

### 4. Async HOF

```javascript
// Async map
async function asyncMap(array, asyncFn) {
    const promises = array.map(asyncFn);
    return Promise.all(promises);
}

// Async filter
async function asyncFilter(array, asyncPredicate) {
    const results = await asyncMap(array, asyncPredicate);
    return array.filter((_, index) => results[index]);
}

// דוגמה לשימוש
async function fetchUser(id) {
    // סימולציה של API call
    return new Promise(resolve => {
        setTimeout(() => {
            resolve({ id, name: `User ${id}`, active: id % 2 === 0 });
        }, 100);
    });
}

async function isActive(user) {
    return user.active;
}

async function example() {
    const userIds = [1, 2, 3, 4, 5];
    
    // טעינת כל המשתמשים
    const users = await asyncMap(userIds, fetchUser);
    console.log('All users:', users);
    
    // סינון רק משתמשים פעילים
    const activeUsers = await asyncFilter(users, isActive);
    console.log('Active users:', activeUsers);
}

example();
```

## Best Practices

### 1. העדף Declarative על Imperative

```javascript
const numbers = [1, 2, 3, 4, 5];

// ❌ Imperative (איך לעשות)
const doubled = [];
for (let i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}

// ✅ Declarative (מה לעשות)
const doubled = numbers.map(n => n * 2);
```

### 2. השתמש ב-Method הנכון

```javascript
const numbers = [1, 2, 3, 4, 5];

// ❌ רע - forEach לא מתאים כאן
const doubled = [];
numbers.forEach(n => doubled.push(n * 2));

// ✅ טוב - map מתאים יותר
const doubled = numbers.map(n => n * 2);

// ❌ רע - map לא מתאים כאן
numbers.map(n => console.log(n)); // מחזיר מערך של undefined!

// ✅ טוב - forEach מתאים יותר
numbers.forEach(n => console.log(n));
```

### 3. שמירה על Pure Functions

```javascript
// ❌ Impure - משנה state חיצוני
let total = 0;
function addToTotal(n) {
    total += n;
    return total;
}

// ✅ Pure - לא משנה state חיצוני
function add(a, b) {
    return a + b;
}
```

### 4. הימנע מ-Side Effects ב-map/filter

```javascript
// ❌ רע
const numbers = [1, 2, 3];
numbers.map(n => {
    console.log(n); // side effect!
    saveToDatabase(n); // side effect!
    return n * 2;
});

// ✅ טוב
numbers.forEach(n => {
    console.log(n);
    saveToDatabase(n);
});
const doubled = numbers.map(n => n * 2);
```

### 5. שימוש נכון ב-reduce

```javascript
// ❌ מסובך מדי
const result = array.reduce((acc, item) => {
    // 50 שורות של לוגיקה...
    return acc;
}, {});

// ✅ פשוט וקריא
const step1 = array.filter(...);
const step2 = step1.map(...);
const result = step2.reduce(...);
```

### 6. שמות ברורים לפונקציות

```javascript
// ❌ רע
const filtered = users.filter(u => u.a >= 18);

// ✅ טוב
const isAdult = user => user.age >= 18;
const adults = users.filter(isAdult);
```

### 7. Composition על Nesting

```javascript
// ❌ קשה לקריאה
const result = func1(func2(func3(func4(data))));

// ✅ קל לקריאה
const process = pipe(func4, func3, func2, func1);
const result = process(data);
```

### 8. הימנע מ-Premature Optimization

```javascript
// ✅ תחילה - קוד קריא
const result = data
    .filter(isValid)
    .map(transform)
    .reduce(aggregate, initial);

// רק אם יש בעיית ביצועים - אופטימיזציה
const result = data.reduce((acc, item) => {
    if (isValid(item)) {
        const transformed = transform(item);
        return aggregate(acc, transformed);
    }
    return acc;
}, initial);
```

## סיכום

### מתי להשתמש במה?

| Method | שימוש | מחזיר |
|--------|-------|-------|
| **map** | המרת כל איבר | מערך חדש באותו אורך |
| **filter** | סינון איברים | מערך חדש (יכול להיות קצר יותר) |
| **reduce** | צבירה לערך אחד | ערך בודד (כל סוג) |
| **forEach** | פעולות עם side effects | undefined |
| **find** | מציאת איבר | האיבר הראשון או undefined |
| **some** | בדיקה אם יש | boolean |
| **every** | בדיקה אם כולם | boolean |
| **sort** | מיון | מערך ממוין (משנה מקור!) |

### עקרונות מנחים:

1. 🎯 **Declarative > Imperative** - תגיד מה, לא איך
2. 🔄 **Pure Functions** - ללא side effects
3. 🧩 **Composition** - שלב פונקציות קטנות
4. 📖 **Readability** - קוד קריא עדיף על חכם
5. ♻️ **Reusability** - פונקציות קטנות ניתנות לשימוש חוזר
6. 🛡️ **Immutability** - אל תשנה את המקור

## תרגילים מומלצים

1. **Array Transformations**: המר מערכי נתונים שונים עם map, filter, reduce
2. **Custom HOF**: צור את ה-map, filter, reduce משלך
3. **Currying Practice**: המר פונקציות רגילות ל-curried functions
4. **Pipeline**: בנה data processing pipeline מורכב
5. **Decorators**: צור decorators שונים (logging, caching, validation)
6. **Real Project**: בנה מערכת סינון למוצרים או משתמשים

בהצלחה! 🚀
