# מדריך למתחילים: Modules ב-JavaScript ו-CommonJS

## תוכן עניינים
1. [מבוא](#מבוא)
2. [למה אנחנו צריכים Modules?](#למה-אנחנו-צריכים-modules)
3. [CommonJS](#commonjs)
4. [ES6 Modules (ESM)](#es6-modules-esm)
5. [הבדלים עיקריים](#הבדלים-עיקריים)
6. [דוגמאות מעשיות](#דוגמאות-מעשיות)
7. [Best Practices](#best-practices)

## מבוא

Modules הם דרך לארגן ולחלק את הקוד שלנו לקבצים נפרדים. זה מאפשר לנו:
- לשמור על קוד מאורגן וקריא
- לעשות שימוש חוזר בקוד
- להימנע מזיהום של ה-Global Scope
- לנהל dependencies בצורה טובה יותר

## למה אנחנו צריכים Modules?

### בלי Modules:
```javascript
// script1.js
var name = "John";
function greet() {
    console.log("Hello " + name);
}

// script2.js
var name = "Jane"; // 😱 מחליף את name מ-script1!
function greet() { // 😱 מחליף את greet מ-script1!
    console.log("Hi " + name);
}
```

**בעיות:**
- כל המשתנים והפונקציות ב-Global Scope
- התנגשויות בשמות (name collisions)
- קשה לנהל dependencies
- קשה לדעת מה תלוי במה

### עם Modules:
```javascript
// user.js
export const name = "John";
export function greet() {
    console.log("Hello " + name);
}

// admin.js
export const name = "Jane"; // ✅ לא מתנגש!
export function greet() { // ✅ לא מתנגש!
    console.log("Hi " + name);
}
```

## CommonJS

CommonJS היא המערכת הישנה יותר, שמשמשת בעיקר ב-Node.js.

### ייצוא (Export)

#### ייצוא בסיסי:
```javascript
// math.js
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

// דרך 1: ייצוא אחד אחד
module.exports.add = add;
module.exports.subtract = subtract;

// דרך 2: ייצוא אובייקט
module.exports = {
    add: add,
    subtract: subtract
};

// דרך 3: ייצוא מקוצר
module.exports = { add, subtract };
```

#### ייצוא ברירת מחדל (Default Export):
```javascript
// calculator.js
class Calculator {
    add(a, b) {
        return a + b;
    }
}

module.exports = Calculator;
```

### ייבוא (Import)

```javascript
// app.js

// ייבוא מלא
const math = require('./math');
console.log(math.add(5, 3)); // 8

// ייבוא עם Destructuring
const { add, subtract } = require('./math');
console.log(add(5, 3)); // 8
console.log(subtract(10, 4)); // 6

// ייבוא של default export
const Calculator = require('./calculator');
const calc = new Calculator();
```

### דוגמה מלאה - CommonJS:

```javascript
// user.js
const generateId = () => Math.random().toString(36).substr(2, 9);

class User {
    constructor(name, email) {
        this.id = generateId();
        this.name = name;
        this.email = email;
    }

    getInfo() {
        return `${this.name} (${this.email})`;
    }
}

module.exports = User;

// admin.js
const User = require('./user');

class Admin extends User {
    constructor(name, email, permissions) {
        super(name, email);
        this.permissions = permissions;
    }

    hasPermission(permission) {
        return this.permissions.includes(permission);
    }
}

module.exports = Admin;

// app.js
const User = require('./user');
const Admin = require('./admin');

const user = new User('John', 'john@example.com');
console.log(user.getInfo()); // John (john@example.com)

const admin = new Admin('Jane', 'jane@example.com', ['read', 'write', 'delete']);
console.log(admin.getInfo()); // Jane (jane@example.com)
console.log(admin.hasPermission('write')); // true
```

## ES6 Modules (ESM)

ES6 Modules הם הסטנדרט המודרני ל-JavaScript, נתמכים בדפדפנים ובגרסאות חדשות של Node.js.

### ייצוא (Export)

#### Named Exports:
```javascript
// math.js

// דרך 1: ייצוא ישיר
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export const PI = 3.14159;

// דרך 2: ייצוא בסוף הקובץ
function multiply(a, b) {
    return a * b;
}

function divide(a, b) {
    return a / b;
}

export { multiply, divide };
```

#### Default Export:
```javascript
// calculator.js
export default class Calculator {
    add(a, b) {
        return a + b;
    }
    
    subtract(a, b) {
        return a - b;
    }
}

// או
class Calculator {
    // ...
}
export default Calculator;

// עבור פונקציה
export default function sum(arr) {
    return arr.reduce((a, b) => a + b, 0);
}
```

#### ערבוב Default ו-Named Exports:
```javascript
// utils.js
export default function mainFunction() {
    console.log("Main function");
}

export const helper1 = () => "Helper 1";
export const helper2 = () => "Helper 2";
```

### ייבוא (Import)

```javascript
// app.js

// ייבוא Named Exports
import { add, subtract, PI } from './math.js';
console.log(add(5, 3)); // 8
console.log(PI); // 3.14159

// ייבוא הכל כאובייקט
import * as math from './math.js';
console.log(math.add(5, 3)); // 8
console.log(math.PI); // 3.14159

// ייבוא Default Export
import Calculator from './calculator.js';
const calc = new Calculator();

// ייבוא עם שם אחר (Alias)
import { add as addition } from './math.js';
console.log(addition(5, 3)); // 8

// ערבוב Default ו-Named
import mainFunction, { helper1, helper2 } from './utils.js';
```

### דוגמה מלאה - ES6 Modules:

```javascript
// constants.js
export const DB_NAME = 'myapp';
export const MAX_USERS = 100;
export const ROLES = {
    ADMIN: 'admin',
    USER: 'user',
    GUEST: 'guest'
};

// utils.js
export const generateId = () => {
    return Math.random().toString(36).substr(2, 9);
};

export const validateEmail = (email) => {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

export const formatDate = (date) => {
    return new Date(date).toLocaleDateString('he-IL');
};

// user.js
import { generateId, validateEmail } from './utils.js';
import { ROLES } from './constants.js';

export default class User {
    constructor(name, email) {
        if (!validateEmail(email)) {
            throw new Error('Invalid email');
        }
        this.id = generateId();
        this.name = name;
        this.email = email;
        this.role = ROLES.USER;
    }

    getInfo() {
        return `${this.name} (${this.email}) - ${this.role}`;
    }
}

// admin.js
import User from './user.js';
import { ROLES } from './constants.js';

export default class Admin extends User {
    constructor(name, email, permissions = []) {
        super(name, email);
        this.role = ROLES.ADMIN;
        this.permissions = permissions;
    }

    hasPermission(permission) {
        return this.permissions.includes(permission);
    }
}

// app.js
import User from './user.js';
import Admin from './admin.js';
import { MAX_USERS, ROLES } from './constants.js';

const user = new User('John', 'john@example.com');
console.log(user.getInfo());

const admin = new Admin('Jane', 'jane@example.com', ['read', 'write']);
console.log(admin.getInfo());
console.log(admin.hasPermission('write')); // true

console.log(`Max users allowed: ${MAX_USERS}`);
```

## הבדלים עיקריים

| תכונה | CommonJS | ES6 Modules |
|-------|----------|-------------|
| **Syntax** | `require()` / `module.exports` | `import` / `export` |
| **Loading** | Synchronous (סינכרוני) | Asynchronous (אסינכרוני) |
| **Timing** | Runtime (בזמן ריצה) | Parse time (בזמן פרסינג) |
| **שימוש ב-Node.js** | Default (ברירת מחדל) | צריך `"type": "module"` ב-package.json או סיומת `.mjs` |
| **שימוש בדפדפן** | לא נתמך ישירות | נתמך (עם `type="module"`) |
| **Dynamic Import** | תמיד dynamic | Static, אבל יש גם `import()` dynamic |
| **Tree Shaking** | לא | כן (הסרת קוד מת) |
| **Top-level await** | לא | כן |

### דוגמאות להבדלים:

#### 1. Loading זמן:
```javascript
// CommonJS - נטען בזמן ריצה
if (condition) {
    const module = require('./module'); // ✅ עובד
}

// ES6 - נטען בזמן פרסינג
if (condition) {
    import module from './module'; // ❌ שגיאה!
}

// אבל אפשר להשתמש ב-dynamic import:
if (condition) {
    const module = await import('./module'); // ✅ עובד
}
```

#### 2. Exports הם reference:
```javascript
// CommonJS - copy
// counter.js
let count = 0;
module.exports.count = count;
module.exports.increment = () => {
    count++;
};

// app.js
const { count, increment } = require('./counter');
console.log(count); // 0
increment();
console.log(count); // עדיין 0! (copy)

// ES6 - reference
// counter.js
export let count = 0;
export const increment = () => {
    count++;
};

// app.js
import { count, increment } from './counter.js';
console.log(count); // 0
increment();
console.log(count); // 1 (reference!)
```

## דוגמאות מעשיות

### דוגמה 1: מערכת לניהול משימות

```javascript
// models/task.js
export default class Task {
    constructor(title, description) {
        this.id = Date.now();
        this.title = title;
        this.description = description;
        this.completed = false;
        this.createdAt = new Date();
    }

    complete() {
        this.completed = true;
    }

    toString() {
        const status = this.completed ? '✅' : '⬜';
        return `${status} ${this.title}`;
    }
}

// services/taskManager.js
import Task from '../models/task.js';

export class TaskManager {
    constructor() {
        this.tasks = [];
    }

    addTask(title, description) {
        const task = new Task(title, description);
        this.tasks.push(task);
        return task;
    }

    removeTask(id) {
        this.tasks = this.tasks.filter(task => task.id !== id);
    }

    completeTask(id) {
        const task = this.tasks.find(task => task.id === id);
        if (task) {
            task.complete();
        }
    }

    getAllTasks() {
        return this.tasks;
    }

    getCompletedTasks() {
        return this.tasks.filter(task => task.completed);
    }

    getPendingTasks() {
        return this.tasks.filter(task => !task.completed);
    }
}

// utils/logger.js
export const log = (message) => {
    console.log(`[${new Date().toISOString()}] ${message}`);
};

export const logError = (error) => {
    console.error(`[${new Date().toISOString()}] ERROR:`, error);
};

// app.js
import { TaskManager } from './services/taskManager.js';
import { log } from './utils/logger.js';

const manager = new TaskManager();

log('Adding tasks...');
manager.addTask('Learn JavaScript', 'Study ES6 modules');
manager.addTask('Build project', 'Create a todo app');

log('Current tasks:');
manager.getAllTasks().forEach(task => {
    console.log(task.toString());
});

manager.completeTask(manager.getAllTasks()[0].id);

log('After completing first task:');
manager.getAllTasks().forEach(task => {
    console.log(task.toString());
});
```

### דוגמה 2: API Client

```javascript
// config/api.js
export const API_CONFIG = {
    baseURL: 'https://jsonplaceholder.typicode.com',
    timeout: 5000,
    headers: {
        'Content-Type': 'application/json'
    }
};

// services/httpClient.js
import { API_CONFIG } from '../config/api.js';

export class HttpClient {
    constructor(config = API_CONFIG) {
        this.baseURL = config.baseURL;
        this.timeout = config.timeout;
        this.headers = config.headers;
    }

    async get(endpoint) {
        const response = await fetch(`${this.baseURL}${endpoint}`, {
            method: 'GET',
            headers: this.headers
        });
        return response.json();
    }

    async post(endpoint, data) {
        const response = await fetch(`${this.baseURL}${endpoint}`, {
            method: 'POST',
            headers: this.headers,
            body: JSON.stringify(data)
        });
        return response.json();
    }
}

// services/userService.js
import { HttpClient } from './httpClient.js';

export class UserService {
    constructor() {
        this.client = new HttpClient();
    }

    async getUsers() {
        return this.client.get('/users');
    }

    async getUserById(id) {
        return this.client.get(`/users/${id}`);
    }

    async createUser(userData) {
        return this.client.post('/users', userData);
    }
}

// app.js
import { UserService } from './services/userService.js';

const userService = new UserService();

// שימוש
async function main() {
    try {
        const users = await userService.getUsers();
        console.log('All users:', users);

        const user = await userService.getUserById(1);
        console.log('User 1:', user);
    } catch (error) {
        console.error('Error:', error);
    }
}

main();
```

## Best Practices

### 1. ארגון קבצים
```
project/
├── src/
│   ├── models/        # מחלקות ומודלים
│   ├── services/      # לוגיקה עסקית
│   ├── utils/         # פונקציות עזר
│   ├── config/        # קבצי הגדרות
│   └── index.js       # נקודת כניסה
├── package.json
└── README.md
```

### 2. שמות ברורים
```javascript
// ✅ טוב
import { validateEmail, formatDate } from './utils.js';
import UserService from './services/userService.js';

// ❌ רע
import { v, f } from './utils.js';
import US from './services/userService.js';
```

### 3. קובץ אחד = אחריות אחת
```javascript
// ✅ טוב - כל קובץ עם אחריות ברורה
// user.js - רק User model
// userService.js - רק User business logic
// userValidator.js - רק User validation

// ❌ רע - הכל בקובץ אחד
// userEverything.js - model + service + validation + ...
```

### 4. Re-exporting למבנה נקי
```javascript
// models/index.js
export { default as User } from './user.js';
export { default as Admin } from './admin.js';
export { default as Task } from './task.js';

// app.js - ייבוא נקי
import { User, Admin, Task } from './models/index.js';
```

### 5. הימנע מ-Circular Dependencies
```javascript
// ❌ רע
// a.js
import { b } from './b.js';
export const a = () => b();

// b.js
import { a } from './a.js';
export const b = () => a();

// ✅ טוב - צור קובץ משותף
// shared.js
export const sharedFunction = () => { /* ... */ };

// a.js
import { sharedFunction } from './shared.js';

// b.js
import { sharedFunction } from './shared.js';
```

### 6. שימוש ב-Dynamic Import לביצועים
```javascript
// טעינה עצלנית של modules כבדים
button.addEventListener('click', async () => {
    const { heavyFunction } = await import('./heavyModule.js');
    heavyFunction();
});
```

### 7. קבועים ב-Constants file
```javascript
// constants.js
export const MAX_LOGIN_ATTEMPTS = 3;
export const SESSION_TIMEOUT = 30 * 60 * 1000; // 30 minutes
export const ERROR_MESSAGES = {
    INVALID_EMAIL: 'כתובת האימייל לא תקינה',
    WEAK_PASSWORD: 'הסיסמה חלשה מדי'
};
```

## שימוש ב-Node.js

### CommonJS (ברירת מחדל):
```javascript
// package.json
{
    "name": "my-app",
    "version": "1.0.0"
}

// app.js
const express = require('express');
```

### ES6 Modules ב-Node.js:

**אופציה 1:** הוסף `"type": "module"` ל-package.json
```json
{
    "name": "my-app",
    "version": "1.0.0",
    "type": "module"
}
```

**אופציה 2:** השתמש בסיומת `.mjs`
```javascript
// app.mjs
import express from 'express';
```

## שימוש בדפדפן

```html
<!-- HTML -->
<!DOCTYPE html>
<html>
<head>
    <title>ES6 Modules Demo</title>
</head>
<body>
    <!-- שים לב ל-type="module" -->
    <script type="module" src="app.js"></script>
</body>
</html>
```

```javascript
// app.js
import { greet } from './utils.js';
greet('World');
```

**⚠️ חשוב:** בדפדפן, חייבים לכלול את סיומת הקובץ `.js` ב-import!

## סיכום

- **CommonJS**: מערכת ישנה יותר, משמשת ב-Node.js, synchronous
- **ES6 Modules**: סטנדרט מודרני, עובד בדפדפנים וב-Node.js, asynchronous
- **Best Practice**: השתמש ב-ES6 Modules בפרויקטים חדשים
- **ארגון**: שמור על מבנה קבצים ברור וארגון טוב
- **Re-exporting**: השתמש בקבצי index.js לייבוא נקי יותר

## תרגילים מומלצים

1. צור מערכת לניהול ספרייה (books, authors, categories)
2. בנה API client עם services שונים (users, posts, comments)
3. צור מערכת validation עם rules שונים
4. בנה calculator מודולרי עם פעולות שונות
5. צור game engine פשוט עם modules נפרדים (player, enemy, game)

בהצלחה! 🚀
