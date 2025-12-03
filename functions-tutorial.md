# מדריך למתחילים: סוגי פונקציות ב-JavaScript

## תוכן עניינים
1. [מבוא](#מבוא)
2. [Function Declaration](#function-declaration)
3. [Function Expression](#function-expression)
4. [Arrow Functions](#arrow-functions)
5. [Anonymous Functions](#anonymous-functions)
6. [IIFE](#iife)
7. [Constructor Functions](#constructor-functions)
8. [Generator Functions](#generator-functions)
9. [Async Functions](#async-functions)
10. [הבדלים ו-Best Practices](#הבדלים-ו-best-practices)

## מבוא

פונקציות הן אחד מאבני היסוד של JavaScript. הן מאפשרות לנו:
- לעשות שימוש חוזר בקוד
- לארגן את הקוד שלנו
- להפוך את הקוד לקריא יותר
- ליצור abstractions

ב-JavaScript יש מספר דרכים להגדיר פונקציות, כל אחת עם המאפיינים והשימושים שלה.

## Function Declaration

זו הדרך הקלאסית והבסיסית ביותר להגדיר פונקציה.

### Syntax:
```javascript
function functionName(parameters) {
    // קוד הפונקציה
    return value;
}
```

### דוגמאות:

```javascript
// פונקציה פשוטה
function greet() {
    console.log("Hello!");
}
greet(); // Hello!

// פונקציה עם פרמטרים
function greetUser(name) {
    console.log(`Hello, ${name}!`);
}
greetUser("John"); // Hello, John!

// פונקציה עם return
function add(a, b) {
    return a + b;
}
const result = add(5, 3);
console.log(result); // 8

// פונקציה עם default parameters
function greetWithDefault(name = "Guest") {
    return `Hello, ${name}!`;
}
console.log(greetWithDefault()); // Hello, Guest!
console.log(greetWithDefault("Alice")); // Hello, Alice!

// פונקציה עם rest parameters
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}
console.log(sum(1, 2, 3, 4, 5)); // 15
```

### מאפיינים חשובים:

#### 1. Hoisting
Function declarations "עולות" לראש הקוד, כך שניתן לקרוא להן לפני ההגדרה:

```javascript
// ✅ עובד!
sayHello(); // Hello!

function sayHello() {
    console.log("Hello!");
}

// זה עובד כי JavaScript "מעלה" את ההגדרה לראש
```

#### 2. Named Function
לפונקציה יש שם, מה שעוזר ב-debugging:

```javascript
function calculateTotal(price, tax) {
    return price + (price * tax);
}

// כאשר יש שגיאה, תראה את שם הפונקציה ב-stack trace
```

### דוגמאות מעשיות:

```javascript
// בדיקת תקינות
function isValidEmail(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

console.log(isValidEmail("user@example.com")); // true
console.log(isValidEmail("invalid-email")); // false

// חישובים
function calculateDiscount(price, discountPercent) {
    const discount = price * (discountPercent / 100);
    return price - discount;
}

console.log(calculateDiscount(100, 20)); // 80

// עבודה עם מערכים
function findMax(numbers) {
    if (numbers.length === 0) return null;
    return Math.max(...numbers);
}

console.log(findMax([3, 7, 2, 9, 1])); // 9

// פונקציה מורכבת יותר
function createUser(name, email, age) {
    if (!name || !email) {
        throw new Error("Name and email are required");
    }
    
    return {
        id: Date.now(),
        name: name,
        email: email,
        age: age || 18,
        createdAt: new Date()
    };
}

const user = createUser("John", "john@example.com", 25);
console.log(user);
```

## Function Expression

פונקציה שמוקצית למשתנה.

### Syntax:
```javascript
const functionName = function(parameters) {
    // קוד הפונקציה
    return value;
};
```

### דוגמאות:

```javascript
// פונקציה אנונימית במשתנה
const greet = function() {
    console.log("Hello!");
};
greet(); // Hello!

// עם פרמטרים
const multiply = function(a, b) {
    return a * b;
};
console.log(multiply(4, 5)); // 20

// Named function expression (עם שם)
const factorial = function fact(n) {
    if (n <= 1) return 1;
    return n * fact(n - 1);
};
console.log(factorial(5)); // 120
```

### הבדל מ-Function Declaration:

#### לא עוברת Hoisting:
```javascript
// ❌ לא עובד!
try {
    sayHi(); // Error: sayHi is not a function
} catch(e) {
    console.log(e.message);
}

const sayHi = function() {
    console.log("Hi!");
};

// ✅ עכשיו עובד
sayHi(); // Hi!
```

### שימושים נפוצים:

```javascript
// כ-callback
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(function(num) {
    return num * 2;
});
console.log(doubled); // [2, 4, 6, 8, 10]

// התניה
const operation = Math.random() > 0.5
    ? function(a, b) { return a + b; }
    : function(a, b) { return a - b; };

console.log(operation(10, 5));

// במבנה אובייקט
const calculator = {
    add: function(a, b) {
        return a + b;
    },
    subtract: function(a, b) {
        return a - b;
    },
    multiply: function(a, b) {
        return a * b;
    }
};

console.log(calculator.add(5, 3)); // 8
```

## Arrow Functions

תחביר מקוצר ומודרני לכתיבת פונקציות (ES6+).

### Syntax:
```javascript
// תחביר בסיסי
const functionName = (parameters) => {
    // קוד
    return value;
};

// תחביר מקוצר (אם יש רק return)
const functionName = (parameters) => value;

// פרמטר אחד (ללא סוגריים)
const functionName = parameter => value;

// בלי פרמטרים
const functionName = () => value;
```

### דוגמאות:

```javascript
// פונקציה פשוטה
const greet = () => console.log("Hello!");
greet(); // Hello!

// עם פרמטר אחד
const square = x => x * x;
console.log(square(5)); // 25

// עם מספר פרמטרים
const add = (a, b) => a + b;
console.log(add(3, 7)); // 10

// עם body מורכב
const calculateArea = (width, height) => {
    const area = width * height;
    console.log(`Area: ${area}`);
    return area;
};
calculateArea(5, 10); // Area: 50

// return של object (שים לב לסוגריים!)
const createPerson = (name, age) => ({
    name: name,
    age: age
});
console.log(createPerson("John", 30)); // { name: 'John', age: 30 }

// עם default parameters
const greetUser = (name = "Guest") => `Hello, ${name}!`;
console.log(greetUser()); // Hello, Guest!

// עם rest parameters
const sum = (...numbers) => numbers.reduce((a, b) => a + b, 0);
console.log(sum(1, 2, 3, 4)); // 10
```

### הבדלים חשובים מפונקציות רגילות:

#### 1. אין `this` משלהן
```javascript
// Function רגילה
const person1 = {
    name: "John",
    greet: function() {
        console.log(`Hello, I'm ${this.name}`);
    }
};
person1.greet(); // Hello, I'm John

// Arrow function - this מתייחס להקשר החיצוני!
const person2 = {
    name: "Jane",
    greet: () => {
        console.log(`Hello, I'm ${this.name}`); // this זה לא person2!
    }
};
person2.greet(); // Hello, I'm undefined

// דוגמה טובה לשימוש ב-arrow function
const person3 = {
    name: "Alice",
    hobbies: ["reading", "coding", "gaming"],
    showHobbies: function() {
        // כאן arrow function עוזרת!
        this.hobbies.forEach(hobby => {
            console.log(`${this.name} likes ${hobby}`);
        });
    }
};
person3.showHobbies();
// Alice likes reading
// Alice likes coding
// Alice likes gaming
```

#### 2. אין `arguments` object
```javascript
// Function רגילה - יש arguments
function regularFunc() {
    console.log(arguments);
}
regularFunc(1, 2, 3); // [1, 2, 3]

// Arrow function - אין arguments (השתמש ב-rest parameters)
const arrowFunc = (...args) => {
    console.log(args);
};
arrowFunc(1, 2, 3); // [1, 2, 3]
```

#### 3. לא ניתן להשתמש כ-constructor
```javascript
// Function רגילה - אפשר
function Person(name) {
    this.name = name;
}
const john = new Person("John"); // ✅ עובד

// Arrow function - לא אפשר
const PersonArrow = (name) => {
    this.name = name;
};
// const jane = new PersonArrow("Jane"); // ❌ Error!
```

### שימושים מצוינים ל-Arrow Functions:

```javascript
// Array methods
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

const evens = numbers.filter(n => n % 2 === 0);
console.log(evens); // [2, 4]

const sum = numbers.reduce((total, n) => total + n, 0);
console.log(sum); // 15

// Callbacks
setTimeout(() => {
    console.log("This runs after 1 second");
}, 1000);

// Event handlers (לפעמים)
const button = { 
    clicked: false,
    click: function() {
        setTimeout(() => {
            this.clicked = true; // this עדיין מצביע על button!
            console.log("Button clicked!");
        }, 100);
    }
};

// Chaining
const users = [
    { name: "John", age: 25 },
    { name: "Jane", age: 30 },
    { name: "Bob", age: 20 }
];

const names = users
    .filter(user => user.age >= 25)
    .map(user => user.name)
    .sort();
console.log(names); // ["Jane", "John"]
```

## Anonymous Functions

פונקציות ללא שם, משמשות בדרך כלל כ-callbacks.

### דוגמאות:

```javascript
// כ-callback ב-setTimeout
setTimeout(function() {
    console.log("This is anonymous!");
}, 1000);

// כ-callback ב-event listener (דמה)
const button = {
    addEventListener: function(event, callback) {
        callback();
    }
};

button.addEventListener("click", function() {
    console.log("Button clicked!");
});

// ב-array methods
const numbers = [1, 2, 3, 4, 5];

numbers.forEach(function(num) {
    console.log(num * 2);
});

// מיון מותאם אישית
const people = [
    { name: "John", age: 30 },
    { name: "Jane", age: 25 },
    { name: "Bob", age: 35 }
];

people.sort(function(a, b) {
    return a.age - b.age;
});
console.log(people);
// [{ name: 'Jane', age: 25 }, { name: 'John', age: 30 }, { name: 'Bob', age: 35 }]
```

## IIFE (Immediately Invoked Function Expression)

פונקציה שרצה מיד לאחר ההגדרה.

### Syntax:
```javascript
(function() {
    // קוד שרץ מיד
})();

// או עם arrow function
(() => {
    // קוד שרץ מיד
})();
```

### דוגמאות:

```javascript
// IIFE בסיסי
(function() {
    console.log("I run immediately!");
})();
// I run immediately!

// עם פרמטרים
(function(name) {
    console.log(`Hello, ${name}!`);
})("World");
// Hello, World!

// עם return value
const result = (function(a, b) {
    return a + b;
})(5, 3);
console.log(result); // 8

// יצירת scope פרטי
(function() {
    const secret = "This is private!";
    console.log(secret);
})();
// console.log(secret); // ❌ Error: secret is not defined

// Module pattern
const counter = (function() {
    let count = 0; // משתנה פרטי
    
    return {
        increment: function() {
            count++;
            return count;
        },
        decrement: function() {
            count--;
            return count;
        },
        getCount: function() {
            return count;
        }
    };
})();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount()); // 2
console.log(counter.decrement()); // 1
// console.log(count); // ❌ Error: count is not defined

// הימנעות מזיהום global scope
(function() {
    const appConfig = {
        apiUrl: "https://api.example.com",
        timeout: 5000
    };
    
    // קוד שמשתמש ב-appConfig
    console.log("App initialized with:", appConfig);
})();
```

### שימושים נפוצים:

```javascript
// 1. אתחול חד-פעמי
(function() {
    console.log("App starting...");
    // אתחול
    const config = loadConfig();
    setupEventListeners();
    console.log("App ready!");
})();

// 2. הגנה על משתנים
const calculator = (function() {
    // משתנים פרטיים
    const PI = 3.14159;
    const E = 2.71828;
    
    // פונקציות פרטיות
    function isNumber(n) {
        return typeof n === 'number' && !isNaN(n);
    }
    
    // API ציבורי
    return {
        add: (a, b) => isNumber(a) && isNumber(b) ? a + b : null,
        circleArea: (r) => PI * r * r,
        exponential: (x) => Math.pow(E, x)
    };
})();

console.log(calculator.add(5, 3)); // 8
console.log(calculator.circleArea(5)); // 78.53975
// console.log(PI); // ❌ Error: PI is not defined
```

## Constructor Functions

פונקציות ליצירת אובייקטים (לפני ES6 classes).

### Syntax:
```javascript
function ConstructorName(parameters) {
    this.property = value;
    this.method = function() {
        // ...
    };
}

const instance = new ConstructorName(arguments);
```

### דוגמאות:

```javascript
// Constructor פשוט
function Person(name, age) {
    this.name = name;
    this.age = age;
}

const john = new Person("John", 30);
console.log(john); // Person { name: 'John', age: 30 }

// עם methods
function Car(brand, model, year) {
    this.brand = brand;
    this.model = model;
    this.year = year;
    
    this.getInfo = function() {
        return `${this.year} ${this.brand} ${this.model}`;
    };
}

const myCar = new Car("Toyota", "Camry", 2020);
console.log(myCar.getInfo()); // 2020 Toyota Camry

// שימוש ב-prototype (יעיל יותר!)
function Animal(name, type) {
    this.name = name;
    this.type = type;
}

// Methods על ה-prototype משותפים לכל ה-instances
Animal.prototype.speak = function() {
    return `${this.name} the ${this.type} makes a sound`;
};

Animal.prototype.getInfo = function() {
    return `Name: ${this.name}, Type: ${this.type}`;
};

const dog = new Animal("Rex", "Dog");
const cat = new Animal("Whiskers", "Cat");

console.log(dog.speak()); // Rex the Dog makes a sound
console.log(cat.speak()); // Whiskers the Cat makes a sound

// Constructor עם validation
function User(username, email, age) {
    // Validation
    if (!username || !email) {
        throw new Error("Username and email are required");
    }
    
    if (age < 0 || age > 150) {
        throw new Error("Invalid age");
    }
    
    // Properties
    this.username = username;
    this.email = email;
    this.age = age;
    this.createdAt = new Date();
    
    // Private-like variable (not truly private)
    let loginAttempts = 0;
    
    // Privileged method (can access loginAttempts)
    this.login = function(password) {
        loginAttempts++;
        console.log(`Login attempt #${loginAttempts}`);
        // login logic...
    };
}

User.prototype.getAge = function() {
    return this.age;
};

const user = new User("john_doe", "john@example.com", 25);
user.login("password123"); // Login attempt #1
user.login("password123"); // Login attempt #2
```

### Inheritance עם Constructors:

```javascript
// Base constructor
function Vehicle(type, wheels) {
    this.type = type;
    this.wheels = wheels;
}

Vehicle.prototype.getInfo = function() {
    return `This is a ${this.type} with ${this.wheels} wheels`;
};

// Derived constructor
function Bicycle(brand, color) {
    Vehicle.call(this, "bicycle", 2); // קריאה ל-parent constructor
    this.brand = brand;
    this.color = color;
}

// הגדרת prototype chain
Bicycle.prototype = Object.create(Vehicle.prototype);
Bicycle.prototype.constructor = Bicycle;

// הוספת method ל-Bicycle
Bicycle.prototype.ring = function() {
    return "Ring ring!";
};

const myBike = new Bicycle("Trek", "blue");
console.log(myBike.getInfo()); // This is a bicycle with 2 wheels
console.log(myBike.ring()); // Ring ring!
console.log(myBike.brand); // Trek
```

## Generator Functions

פונקציות שיכולות להשהות וולחדש את הריצה שלהן.

### Syntax:
```javascript
function* generatorName() {
    yield value1;
    yield value2;
    return finalValue;
}
```

### דוגמאות:

```javascript
// Generator בסיסי
function* simpleGenerator() {
    yield 1;
    yield 2;
    yield 3;
}

const gen = simpleGenerator();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// שימוש ב-for...of
function* countToFive() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    yield 5;
}

for (const num of countToFive()) {
    console.log(num); // 1, 2, 3, 4, 5
}

// Generator עם לולאה
function* countTo(max) {
    for (let i = 1; i <= max; i++) {
        yield i;
    }
}

const counter = countTo(3);
console.log([...counter]); // [1, 2, 3]

// Generator אינסופי
function* infiniteSequence() {
    let i = 0;
    while (true) {
        yield i++;
    }
}

const seq = infiniteSequence();
console.log(seq.next().value); // 0
console.log(seq.next().value); // 1
console.log(seq.next().value); // 2

// ID generator
function* idGenerator() {
    let id = 1;
    while (true) {
        yield id++;
    }
}

const getId = idGenerator();
console.log(getId.next().value); // 1
console.log(getId.next().value); // 2
console.log(getId.next().value); // 3

// Fibonacci generator
function* fibonacci() {
    let [prev, curr] = [0, 1];
    while (true) {
        yield curr;
        [prev, curr] = [curr, prev + curr];
    }
}

const fib = fibonacci();
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
console.log(fib.next().value); // 3
console.log(fib.next().value); // 5
console.log(fib.next().value); // 8

// Generator עם פרמטרים ב-next()
function* withParameters() {
    const x = yield "Give me a number";
    const y = yield "Give me another number";
    return x + y;
}

const paramGen = withParameters();
console.log(paramGen.next());      // { value: 'Give me a number', done: false }
console.log(paramGen.next(10));    // { value: 'Give me another number', done: false }
console.log(paramGen.next(5));     // { value: 15, done: true }

// מעבר על object properties
function* objectEntries(obj) {
    for (const key in obj) {
        if (obj.hasOwnProperty(key)) {
            yield [key, obj[key]];
        }
    }
}

const person = { name: "John", age: 30, city: "New York" };
for (const [key, value] of objectEntries(person)) {
    console.log(`${key}: ${value}`);
}
// name: John
// age: 30
// city: New York
```

### שימושים מעשיים:

```javascript
// 1. Pagination
function* paginate(items, pageSize) {
    for (let i = 0; i < items.length; i += pageSize) {
        yield items.slice(i, i + pageSize);
    }
}

const items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const pages = paginate(items, 3);

console.log(pages.next().value); // [1, 2, 3]
console.log(pages.next().value); // [4, 5, 6]
console.log(pages.next().value); // [7, 8, 9]
console.log(pages.next().value); // [10]

// 2. Range
function* range(start, end, step = 1) {
    for (let i = start; i <= end; i += step) {
        yield i;
    }
}

console.log([...range(1, 10)]); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log([...range(0, 20, 5)]); // [0, 5, 10, 15, 20]

// 3. Tree traversal
function* traverseTree(node) {
    yield node.value;
    if (node.children) {
        for (const child of node.children) {
            yield* traverseTree(child);
        }
    }
}

const tree = {
    value: 1,
    children: [
        { value: 2, children: [{ value: 4 }, { value: 5 }] },
        { value: 3 }
    ]
};

console.log([...traverseTree(tree)]); // [1, 2, 4, 5, 3]
```

## Async Functions

פונקציות לטיפול בפעולות אסינכרוניות (ES2017+).

### Syntax:
```javascript
async function functionName() {
    const result = await asyncOperation();
    return result;
}
```

### דוגמאות:

```javascript
// Async function בסיסית
async function fetchData() {
    return "Data loaded!";
}

fetchData().then(data => console.log(data)); // Data loaded!

// עם await
async function getData() {
    const data = await fetchData();
    console.log(data); // Data loaded!
}
getData();

// טיפול בשגיאות
async function fetchUser(id) {
    try {
        const response = await fetch(`https://api.example.com/users/${id}`);
        const user = await response.json();
        return user;
    } catch (error) {
        console.error("Error fetching user:", error);
        throw error;
    }
}

// מספר awaits
async function processData() {
    try {
        const user = await fetchUser(1);
        const posts = await fetchUserPosts(user.id);
        const comments = await fetchPostComments(posts[0].id);
        
        return { user, posts, comments };
    } catch (error) {
        console.error("Error:", error);
    }
}

// Promise.all עם async/await
async function fetchMultipleUsers(ids) {
    try {
        const promises = ids.map(id => fetchUser(id));
        const users = await Promise.all(promises);
        return users;
    } catch (error) {
        console.error("Error fetching users:", error);
    }
}

// דוגמה עם setTimeout
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function slowFunction() {
    console.log("Starting...");
    await delay(2000);
    console.log("2 seconds passed");
    await delay(1000);
    console.log("1 more second passed");
    return "Done!";
}

slowFunction().then(result => console.log(result));

// Arrow async function
const fetchAndProcess = async () => {
    const data = await fetchData();
    return data.toUpperCase();
};

// Async function expression
const loadUser = async function(id) {
    const user = await fetchUser(id);
    return user;
};
```

### שימושים מעשיים:

```javascript
// 1. API calls
async function getUserProfile(userId) {
    try {
        const response = await fetch(`https://api.example.com/users/${userId}`);
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const user = await response.json();
        return user;
    } catch (error) {
        console.error("Failed to fetch user:", error);
        return null;
    }
}

// 2. טעינה מקבילית
async function loadDashboard(userId) {
    try {
        // רץ במקביל!
        const [user, posts, notifications] = await Promise.all([
            fetchUser(userId),
            fetchUserPosts(userId),
            fetchNotifications(userId)
        ]);
        
        return {
            user,
            posts,
            notifications
        };
    } catch (error) {
        console.error("Error loading dashboard:", error);
    }
}

// 3. Retry logic
async function fetchWithRetry(url, maxRetries = 3) {
    for (let i = 0; i < maxRetries; i++) {
        try {
            const response = await fetch(url);
            if (response.ok) {
                return await response.json();
            }
        } catch (error) {
            console.log(`Attempt ${i + 1} failed`);
            if (i === maxRetries - 1) throw error;
            await delay(1000 * (i + 1)); // exponential backoff
        }
    }
}

// 4. Sequential processing
async function processItems(items) {
    const results = [];
    
    for (const item of items) {
        const result = await processItem(item);
        results.push(result);
    }
    
    return results;
}

// 5. Batch processing
async function batchProcess(items, batchSize = 5) {
    const results = [];
    
    for (let i = 0; i < items.length; i += batchSize) {
        const batch = items.slice(i, i + batchSize);
        const batchResults = await Promise.all(
            batch.map(item => processItem(item))
        );
        results.push(...batchResults);
    }
    
    return results;
}

// 6. Timeout wrapper
async function withTimeout(promise, timeoutMs) {
    const timeout = new Promise((_, reject) => {
        setTimeout(() => reject(new Error("Operation timed out")), timeoutMs);
    });
    
    return Promise.race([promise, timeout]);
}

// שימוש:
async function example() {
    try {
        const data = await withTimeout(fetchData(), 5000);
        console.log(data);
    } catch (error) {
        console.error(error.message);
    }
}
```

### Async Generators:

```javascript
// שילוב של async ו-generator
async function* asyncGenerator() {
    yield await Promise.resolve(1);
    yield await Promise.resolve(2);
    yield await Promise.resolve(3);
}

async function useAsyncGenerator() {
    for await (const value of asyncGenerator()) {
        console.log(value); // 1, 2, 3
    }
}

// דוגמה מעשית - streaming data
async function* fetchPages(url, totalPages) {
    for (let page = 1; page <= totalPages; page++) {
        const response = await fetch(`${url}?page=${page}`);
        const data = await response.json();
        yield data;
    }
}

async function processAllPages() {
    for await (const pageData of fetchPages("https://api.example.com/items", 5)) {
        console.log("Processing page:", pageData);
    }
}
```

## הבדלים ו-Best Practices

### טבלת השוואה:

| סוג | Hoisting | `this` | `arguments` | Constructor | Use Case |
|-----|----------|--------|-------------|-------------|----------|
| **Function Declaration** | ✅ כן | ✅ יש | ✅ יש | ✅ כן | פונקציות כלליות, methods |
| **Function Expression** | ❌ לא | ✅ יש | ✅ יש | ✅ כן | callbacks, conditional functions |
| **Arrow Function** | ❌ לא | ❌ אין | ❌ אין | ❌ לא | callbacks, array methods |
| **Constructor** | ✅ כן | ✅ יש | ✅ יש | ✅ כן | יצירת objects (ישן) |
| **Generator** | ✅ כן | ✅ יש | ✅ יש | ❌ לא | iterators, lazy evaluation |
| **Async** | ❌ לא* | ✅ יש | ✅ יש | ❌ לא | פעולות אסינכרוניות |

*תלוי אם זה declaration או expression

### מתי להשתמש במה?

#### Function Declaration
```javascript
// ✅ טוב עבור:
// - פונקציות עיקריות ב-module
// - פונקציות שצריכות להיות נגישות לפני ההגדרה
// - פונקציות עם שם ברור לצורך debugging

function calculateTotal(items) {
    return items.reduce((sum, item) => sum + item.price, 0);
}

function validateForm(formData) {
    // validation logic
}
```

#### Arrow Functions
```javascript
// ✅ טוב עבור:
// - Array methods (map, filter, reduce)
// - Callbacks קצרים
// - כאשר רוצים לשמור על this מההקשר החיצוני

const numbers = [1, 2, 3, 4, 5];

// מצוין!
const doubled = numbers.map(n => n * 2);

// מצוין עם this!
class Timer {
    constructor() {
        this.seconds = 0;
        setInterval(() => {
            this.seconds++; // this מצביע על Timer instance
        }, 1000);
    }
}

// ❌ לא טוב עבור:
// - Object methods שצריכים this
const person = {
    name: "John",
    greet: () => {
        console.log(`Hi, I'm ${this.name}`); // ❌ this לא מצביע על person
    }
};

// ✅ במקום זאת:
const person2 = {
    name: "John",
    greet() {
        console.log(`Hi, I'm ${this.name}`);
    }
};
```

#### Async/Await
```javascript
// ✅ טוב עבור:
// - API calls
// - פעולות אסינכרוניות שצריכות לרוץ ברצף
// - קוד שצריך להיות קריא

async function loadUserData(userId) {
    const user = await fetchUser(userId);
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    
    return { user, posts, comments };
}

// ✅ עם טיפול בשגיאות
async function safeApiCall() {
    try {
        const data = await fetchData();
        return data;
    } catch (error) {
        console.error("Error:", error);
        return null;
    }
}
```

### Best Practices כלליות:

#### 1. שמות תיאוריים
```javascript
// ✅ טוב
function calculateUserAge(birthDate) { }
function isValidEmail(email) { }
function getUserById(id) { }

// ❌ רע
function calc(bd) { }
function check(e) { }
function get(i) { }
```

#### 2. פונקציה אחת = אחריות אחת
```javascript
// ✅ טוב
function validateEmail(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

function validateAge(age) {
    return age >= 18 && age <= 120;
}

function validateUser(user) {
    return validateEmail(user.email) && validateAge(user.age);
}

// ❌ רע - פונקציה שעושה יותר מדי
function validateAndSaveUser(user) {
    // validation
    // saving to DB
    // sending email
    // logging
    // ...
}
```

#### 3. Default parameters
```javascript
// ✅ טוב
function greet(name = "Guest", greeting = "Hello") {
    return `${greeting}, ${name}!`;
}

// במקום
function greet(name, greeting) {
    name = name || "Guest";
    greeting = greeting || "Hello";
    return `${greeting}, ${name}!`;
}
```

#### 4. Rest parameters במקום arguments
```javascript
// ✅ טוב
function sum(...numbers) {
    return numbers.reduce((a, b) => a + b, 0);
}

// במקום
function sum() {
    return Array.from(arguments).reduce((a, b) => a + b, 0);
}
```

#### 5. Early return
```javascript
// ✅ טוב
function processUser(user) {
    if (!user) return null;
    if (!user.email) return null;
    if (!validateEmail(user.email)) return null;
    
    // עיבוד...
    return processedUser;
}

// במקום
function processUser(user) {
    if (user) {
        if (user.email) {
            if (validateEmail(user.email)) {
                // עיבוד...
                return processedUser;
            }
        }
    }
    return null;
}
```

#### 6. Pure functions כאשר אפשר
```javascript
// ✅ Pure function - תמיד מחזירה אותו פלט לאותו קלט
function add(a, b) {
    return a + b;
}

function calculateDiscount(price, percent) {
    return price * (percent / 100);
}

// ❌ Impure - תלויה במשתנים חיצוניים או משנה state
let total = 0;
function addToTotal(amount) {
    total += amount; // משנה state חיצוני
    return total;
}
```

## תרגילים מומלצים

1. **המרת Function Types**: קח קוד עם function declarations והמר אותו ל-arrow functions (והיפך)
2. **יצירת Module Pattern**: השתמש ב-IIFE ליצירת module עם API ציבורי ופרטי
3. **Async Practice**: צור פונקציות async שמבצעות מספר API calls במקביל
4. **Generator למשחק**: צור generator שמנהל תורות במשחק
5. **Callback Hell to Async/Await**: המר קוד עם callbacks מקוננים ל-async/await

## סיכום

- **Function Declaration**: הדרך הקלאסית, עם hoisting, מצוינת לפונקציות עיקריות
- **Function Expression**: פונקציה במשתנה, טובה ל-callbacks והקצאה מותנית
- **Arrow Functions**: תחביר מקוצר, אידיאלי ל-callbacks ואין להן `this` משלהן
- **Anonymous Functions**: בעיקר ל-callbacks חד-פעמיים
- **IIFE**: ריצה מיידית, טוב ל-initialization ויצירת scope פרטי
- **Constructor Functions**: יצירת objects (ישן יותר, עדיף classes)
- **Generator Functions**: יצירת iterators ו-lazy evaluation
- **Async Functions**: טיפול בפעולות אסינכרוניות בצורה קריאה

בחר את סוג הפונקציה המתאים לצרכים שלך! 🚀
