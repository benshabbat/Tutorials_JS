# מדריך למתחילים: טיפול בשגיאות (Error Handling) ב-JavaScript

## תוכן עניינים
1. [מבוא](#מבוא)
2. [סוגי שגיאות](#סוגי-שגיאות)
3. [Try-Catch-Finally](#try-catch-finally)
4. [Throw - זריקת שגיאות](#throw---זריקת-שגיאות)
5. [Custom Errors](#custom-errors)
6. [Error Handling ב-Async Code](#error-handling-ב-async-code)
7. [Best Practices](#best-practices)
8. [דוגמאות מעשיות](#דוגמאות-מעשיות)

## מבוא

טיפול בשגיאות הוא חלק קריטי בכתיבת קוד איכותי. שגיאות יכולות להתרחש מסיבות רבות:
- 🐛 **Bugs בקוד**
- 📝 **קלט לא תקין מהמשתמש**
- 🌐 **בעיות רשת (network)**
- 💾 **בעיות במסד נתונים**
- 📂 **קבצים שלא נמצאו**

### למה זה חשוב?

```javascript
// ❌ בלי טיפול בשגיאות
function divide(a, b) {
    return a / b;
}

console.log(divide(10, 0)); // Infinity (לא שגיאה, אבל לא רצוי!)
console.log(divide(10, "hello")); // NaN (בעיה!)

// ✅ עם טיפול בשגיאות
function safeDivide(a, b) {
    if (typeof a !== 'number' || typeof b !== 'number') {
        throw new Error('Both arguments must be numbers');
    }
    if (b === 0) {
        throw new Error('Cannot divide by zero');
    }
    return a / b;
}

try {
    console.log(safeDivide(10, 2)); // 5
    console.log(safeDivide(10, 0)); // Error!
} catch (error) {
    console.error('Error:', error.message);
}
```

## סוגי שגיאות

JavaScript מספקת מספר סוגים מובנים של שגיאות:

### 1. Error - שגיאה כללית

```javascript
// יצירת שגיאה כללית
const error = new Error('Something went wrong');
console.log(error.message); // Something went wrong
console.log(error.name); // Error
console.log(error.stack); // Stack trace

// זריקת שגיאה
throw new Error('This is an error');
```

### 2. SyntaxError - שגיאת תחביר

```javascript
// שגיאה שמתגלה בזמן parsing
try {
    eval('Hello World'); // לא JavaScript תקין
} catch (error) {
    console.log(error.name); // SyntaxError
    console.log(error.message);
}

// דוגמאות נוספות:
// const x = ; // SyntaxError
// if (true { } // SyntaxError - חסר )
// function test( { } // SyntaxError
```

### 3. ReferenceError - משתנה לא קיים

```javascript
try {
    console.log(nonExistentVariable); // משתנה שלא הוגדר
} catch (error) {
    console.log(error.name); // ReferenceError
    console.log(error.message); // nonExistentVariable is not defined
}

// דוגמאות נוספות:
try {
    myFunction(); // פונקציה שלא קיימת
} catch (error) {
    console.log(error.name); // ReferenceError
}
```

### 4. TypeError - שגיאת טיפוס

```javascript
// ניסיון לבצע פעולה על טיפוס לא מתאים
try {
    const num = 5;
    num.toUpperCase(); // מספר אין לו toUpperCase
} catch (error) {
    console.log(error.name); // TypeError
    console.log(error.message); // num.toUpperCase is not a function
}

// דוגמאות נוספות:
try {
    null.property; // TypeError
} catch (error) {
    console.log(error.message); // Cannot read property of null
}

try {
    const obj = {};
    obj.method(); // TypeError - method is not a function
} catch (error) {
    console.log(error.message);
}

try {
    const x = Symbol();
    x + 1; // TypeError - Cannot convert Symbol to number
} catch (error) {
    console.log(error.message);
}
```

### 5. RangeError - ערך מחוץ לטווח

```javascript
// ערך שלא בטווח המותר
try {
    const arr = new Array(-1); // גודל שלילי
} catch (error) {
    console.log(error.name); // RangeError
    console.log(error.message); // Invalid array length
}

try {
    const num = 1;
    num.toFixed(-1); // דיוק שלילי
} catch (error) {
    console.log(error.name); // RangeError
}

try {
    function recursion() {
        recursion();
    }
    recursion(); // RangeError: Maximum call stack size exceeded
} catch (error) {
    console.log(error.message);
}
```

### 6. URIError - שגיאה ב-URI encoding/decoding

```javascript
try {
    decodeURIComponent('%'); // URI לא תקין
} catch (error) {
    console.log(error.name); // URIError
    console.log(error.message); // URI malformed
}

try {
    decodeURI('%E0%A4%A'); // URI לא שלם
} catch (error) {
    console.log(error.name); // URIError
}
```

### 7. EvalError - שגיאה ב-eval (נדיר)

```javascript
// שגיאה זו כבר לא נזרקת בגרסאות מודרניות
// אבל עדיין קיימת למטרות תאימות לאחור
```

### סיכום סוגי השגיאות:

```javascript
const errors = {
    Error: new Error('Generic error'),
    SyntaxError: new SyntaxError('Syntax error'),
    ReferenceError: new ReferenceError('Reference error'),
    TypeError: new TypeError('Type error'),
    RangeError: new RangeError('Range error'),
    URIError: new URIError('URI error')
};

Object.entries(errors).forEach(([name, error]) => {
    console.log(`${name}:`, error.message);
});
```

## Try-Catch-Finally

המבנה הבסיסי לטיפול בשגיאות.

### Try-Catch בסיסי

```javascript
// תחביר בסיסי
try {
    // קוד שעלול לזרוק שגיאה
    const result = riskyOperation();
} catch (error) {
    // טיפול בשגיאה
    console.error('An error occurred:', error.message);
}

// דוגמה מעשית
function parseJSON(jsonString) {
    try {
        return JSON.parse(jsonString);
    } catch (error) {
        console.error('Invalid JSON:', error.message);
        return null;
    }
}

console.log(parseJSON('{"name": "John"}')); // { name: 'John' }
console.log(parseJSON('invalid json')); // null
```

### Try-Catch-Finally

```javascript
// Finally רץ תמיד, בלי קשר אם הייתה שגיאה או לא
function processFile(filename) {
    let file = null;
    
    try {
        file = openFile(filename); // פותח קובץ
        const content = readFile(file);
        return content;
    } catch (error) {
        console.error('Error processing file:', error.message);
        return null;
    } finally {
        // תמיד יבוצע - גם אם הייתה שגיאה וגם אם לא
        if (file) {
            closeFile(file); // סוגר את הקובץ
            console.log('File closed');
        }
    }
}

// דוגמה עם ספירה
function countOperations() {
    let counter = 0;
    
    try {
        counter++;
        console.log('Try block');
        throw new Error('Oops!');
        counter++; // לא יגיע לכאן
    } catch (error) {
        counter++;
        console.log('Catch block');
    } finally {
        counter++;
        console.log('Finally block');
    }
    
    console.log('Counter:', counter); // 3
}

countOperations();
// Try block
// Catch block
// Finally block
// Counter: 3
```

### Finally עם Return

```javascript
// Finally רץ גם עם return!
function testFinally() {
    try {
        console.log('Try');
        return 'from try';
    } finally {
        console.log('Finally'); // זה ירוץ לפני ה-return!
    }
}

console.log(testFinally());
// Try
// Finally
// from try

// Finally יכול לשנות את ה-return value!
function overrideReturn() {
    try {
        return 'original';
    } finally {
        return 'overridden'; // ⚠️ משנה את הערך המוחזר
    }
}

console.log(overrideReturn()); // 'overridden'
```

### Nested Try-Catch

```javascript
// try-catch מקוננים
function complexOperation() {
    try {
        console.log('Outer try');
        
        try {
            console.log('Inner try');
            throw new Error('Inner error');
        } catch (innerError) {
            console.log('Inner catch:', innerError.message);
            throw new Error('Re-throwing from inner catch');
        }
        
    } catch (outerError) {
        console.log('Outer catch:', outerError.message);
    }
}

complexOperation();
// Outer try
// Inner try
// Inner catch: Inner error
// Outer catch: Re-throwing from inner catch
```

### Conditional Catch

```javascript
// טיפול שונה לפי סוג השגיאה
function handleError(operation) {
    try {
        operation();
    } catch (error) {
        if (error instanceof TypeError) {
            console.error('Type Error:', error.message);
        } else if (error instanceof ReferenceError) {
            console.error('Reference Error:', error.message);
        } else if (error instanceof RangeError) {
            console.error('Range Error:', error.message);
        } else {
            console.error('Unknown Error:', error.message);
        }
    }
}

// שימוש
handleError(() => {
    const x = null;
    x.toString(); // TypeError
});

handleError(() => {
    console.log(undefinedVariable); // ReferenceError
});
```

## Throw - זריקת שגיאות

יצירה וזריקה של שגיאות מותאמות אישית.

### Throw בסיסי

```javascript
// זריקת שגיאה פשוטה
function checkAge(age) {
    if (age < 0) {
        throw new Error('Age cannot be negative');
    }
    if (age > 150) {
        throw new Error('Age seems unrealistic');
    }
    return age;
}

try {
    console.log(checkAge(25)); // 25
    console.log(checkAge(-5)); // Error!
} catch (error) {
    console.error(error.message);
}
```

### זריקת ערכים שונים

```javascript
// אפשר לזרוק כל ערך (לא רק Error objects)
try {
    throw 'Simple string error';
} catch (error) {
    console.log(typeof error); // string
    console.log(error); // Simple string error
}

try {
    throw 42;
} catch (error) {
    console.log(typeof error); // number
    console.log(error); // 42
}

try {
    throw { message: 'Custom error', code: 500 };
} catch (error) {
    console.log(error.message); // Custom error
    console.log(error.code); // 500
}

// ⚠️ אבל מומלץ תמיד לזרוק Error objects!
```

### למה Error Objects עדיפים?

```javascript
// ✅ טוב - עם Error object
function goodExample() {
    throw new Error('Something went wrong');
}

try {
    goodExample();
} catch (error) {
    console.log(error.name); // Error
    console.log(error.message); // Something went wrong
    console.log(error.stack); // Stack trace מלא!
}

// ❌ פחות טוב - בלי Error object
function badExample() {
    throw 'Something went wrong';
}

try {
    badExample();
} catch (error) {
    console.log(error.name); // undefined
    console.log(error.message); // undefined
    console.log(error.stack); // undefined
}
```

### Input Validation

```javascript
// validation עם throw
function createUser(name, email, age) {
    // בדיקת name
    if (!name || typeof name !== 'string') {
        throw new TypeError('Name must be a non-empty string');
    }
    
    if (name.length < 2) {
        throw new RangeError('Name must be at least 2 characters');
    }
    
    // בדיקת email
    if (!email || typeof email !== 'string') {
        throw new TypeError('Email must be a non-empty string');
    }
    
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(email)) {
        throw new Error('Invalid email format');
    }
    
    // בדיקת age
    if (typeof age !== 'number') {
        throw new TypeError('Age must be a number');
    }
    
    if (age < 0 || age > 150) {
        throw new RangeError('Age must be between 0 and 150');
    }
    
    return {
        id: Date.now(),
        name,
        email,
        age,
        createdAt: new Date()
    };
}

// שימוש
try {
    const user1 = createUser('John Doe', 'john@example.com', 30);
    console.log('User created:', user1);
    
    const user2 = createUser('', 'invalid', -5); // Error!
} catch (error) {
    console.error(`${error.name}: ${error.message}`);
}
```

## Custom Errors

יצירת סוגי שגיאות מותאמים אישית.

### שגיאה מותאמת בסיסית

```javascript
// יצירת Error class משלנו
class ValidationError extends Error {
    constructor(message) {
        super(message);
        this.name = 'ValidationError';
    }
}

// שימוש
function validateEmail(email) {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!regex.test(email)) {
        throw new ValidationError('Invalid email format');
    }
    return true;
}

try {
    validateEmail('invalid-email');
} catch (error) {
    if (error instanceof ValidationError) {
        console.log('Validation failed:', error.message);
    }
}
```

### שגיאות מורכבות יותר

```javascript
// שגיאה עם פרטים נוספים
class DatabaseError extends Error {
    constructor(message, query, code) {
        super(message);
        this.name = 'DatabaseError';
        this.query = query;
        this.code = code;
        this.timestamp = new Date();
    }
}

class NetworkError extends Error {
    constructor(message, statusCode, url) {
        super(message);
        this.name = 'NetworkError';
        this.statusCode = statusCode;
        this.url = url;
        this.timestamp = new Date();
    }
}

class AuthenticationError extends Error {
    constructor(message, userId) {
        super(message);
        this.name = 'AuthenticationError';
        this.userId = userId;
        this.timestamp = new Date();
    }
}

// שימוש
function fetchUser(id) {
    if (id < 0) {
        throw new ValidationError('User ID must be positive');
    }
    
    // סימולציה של בדיקת אימות
    const isAuthenticated = false;
    if (!isAuthenticated) {
        throw new AuthenticationError('User not authenticated', id);
    }
    
    // סימולציה של שגיאת רשת
    throw new NetworkError(
        'Failed to fetch user',
        404,
        `https://api.example.com/users/${id}`
    );
}

// טיפול בשגיאות שונות
try {
    fetchUser(123);
} catch (error) {
    if (error instanceof ValidationError) {
        console.log('Validation Error:', error.message);
    } else if (error instanceof AuthenticationError) {
        console.log('Auth Error:', error.message);
        console.log('User ID:', error.userId);
    } else if (error instanceof NetworkError) {
        console.log('Network Error:', error.message);
        console.log('Status Code:', error.statusCode);
        console.log('URL:', error.url);
    } else if (error instanceof DatabaseError) {
        console.log('Database Error:', error.message);
        console.log('Query:', error.query);
    } else {
        console.log('Unknown Error:', error.message);
    }
}
```

### Error Factory Pattern

```javascript
// מפעל לשגיאות
class ErrorFactory {
    static createValidationError(field, value) {
        return new ValidationError(
            `Invalid ${field}: ${value}`
        );
    }
    
    static createNotFoundError(resource, id) {
        const error = new Error(`${resource} with ID ${id} not found`);
        error.name = 'NotFoundError';
        error.statusCode = 404;
        return error;
    }
    
    static createUnauthorizedError(action) {
        const error = new Error(`Unauthorized to perform: ${action}`);
        error.name = 'UnauthorizedError';
        error.statusCode = 401;
        return error;
    }
}

// שימוש
function getUser(userId) {
    if (typeof userId !== 'number') {
        throw ErrorFactory.createValidationError('userId', userId);
    }
    
    // סימולציה של משתמש לא נמצא
    if (userId > 1000) {
        throw ErrorFactory.createNotFoundError('User', userId);
    }
    
    return { id: userId, name: 'John Doe' };
}

try {
    console.log(getUser('abc')); // ValidationError
} catch (error) {
    console.log(error.message);
}

try {
    console.log(getUser(1001)); // NotFoundError
} catch (error) {
    console.log(error.message);
}
```

### Hierarchical Errors

```javascript
// היררכיה של שגיאות
class AppError extends Error {
    constructor(message) {
        super(message);
        this.name = this.constructor.name;
        this.isOperational = true; // שגיאה צפויה
    }
}

class ClientError extends AppError {
    constructor(message, statusCode = 400) {
        super(message);
        this.statusCode = statusCode;
    }
}

class ServerError extends AppError {
    constructor(message, statusCode = 500) {
        super(message);
        this.statusCode = statusCode;
    }
}

// Client Errors (400s)
class BadRequestError extends ClientError {
    constructor(message) {
        super(message, 400);
    }
}

class UnauthorizedError extends ClientError {
    constructor(message) {
        super(message, 401);
    }
}

class NotFoundError extends ClientError {
    constructor(message) {
        super(message, 404);
    }
}

// Server Errors (500s)
class InternalServerError extends ServerError {
    constructor(message) {
        super(message, 500);
    }
}

class ServiceUnavailableError extends ServerError {
    constructor(message) {
        super(message, 503);
    }
}

// שימוש
function handleRequest(endpoint, userId) {
    if (!userId) {
        throw new BadRequestError('User ID is required');
    }
    
    if (!isAuthenticated()) {
        throw new UnauthorizedError('Please log in');
    }
    
    const user = findUser(userId);
    if (!user) {
        throw new NotFoundError(`User ${userId} not found`);
    }
    
    return user;
}

// Error Handler מרכזי
function errorHandler(error) {
    if (error instanceof ClientError) {
        console.log(`Client Error [${error.statusCode}]:`, error.message);
        // הצג הודעה למשתמש
    } else if (error instanceof ServerError) {
        console.log(`Server Error [${error.statusCode}]:`, error.message);
        // שלח לוג לשרת
        // הצג הודעה כללית למשתמש
    } else {
        console.log('Unexpected Error:', error);
        // טיפול בשגיאות לא צפויות
    }
}

function isAuthenticated() {
    return false; // סימולציה
}

function findUser(id) {
    return null; // סימולציה
}

try {
    handleRequest('/api/users', null);
} catch (error) {
    errorHandler(error);
}
```

## Error Handling ב-Async Code

טיפול בשגיאות בקוד אסינכרוני.

### Promises עם .catch()

```javascript
// Promise שנכשל
function fetchData(url) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (!url) {
                reject(new Error('URL is required'));
            } else if (url.includes('error')) {
                reject(new Error('Failed to fetch data'));
            } else {
                resolve({ data: 'Success!' });
            }
        }, 1000);
    });
}

// טיפול בשגיאה עם .catch()
fetchData('https://api.example.com')
    .then(result => {
        console.log('Data:', result.data);
    })
    .catch(error => {
        console.error('Error:', error.message);
    });

// טיפול בשגיאה עם .then() השני
fetchData('https://api.example.com')
    .then(
        result => console.log('Success:', result.data),
        error => console.error('Error:', error.message)
    );

// שרשור עם .catch()
fetchData('https://api.example.com/users')
    .then(users => fetchData(`https://api.example.com/users/${users[0].id}`))
    .then(user => fetchData(`https://api.example.com/posts/${user.id}`))
    .then(posts => console.log('Posts:', posts))
    .catch(error => {
        console.error('Error in chain:', error.message);
    });
```

### Async/Await עם Try-Catch

```javascript
// async function עם try-catch
async function loadUserData(userId) {
    try {
        const user = await fetchUser(userId);
        const posts = await fetchPosts(user.id);
        const comments = await fetchComments(posts[0].id);
        
        return {
            user,
            posts,
            comments
        };
    } catch (error) {
        console.error('Error loading user data:', error.message);
        throw error; // זריקה מחדש אם צריך
    }
}

// שימוש
async function main() {
    try {
        const data = await loadUserData(123);
        console.log('Data loaded:', data);
    } catch (error) {
        console.error('Failed to load data:', error.message);
    }
}

main();

// פונקציות עזר
async function fetchUser(id) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (id < 0) {
                reject(new Error('Invalid user ID'));
            }
            resolve({ id, name: 'John Doe' });
        }, 100);
    });
}

async function fetchPosts(userId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([{ id: 1, userId, title: 'Post 1' }]);
        }, 100);
    });
}

async function fetchComments(postId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([{ id: 1, postId, text: 'Comment 1' }]);
        }, 100);
    });
}
```

### Multiple Async Operations

```javascript
// טיפול במספר פעולות אסינכרוניות
async function loadMultipleUsers(userIds) {
    const results = {
        successful: [],
        failed: []
    };
    
    // Promise.all - נכשל אם אחת נכשלת
    try {
        const users = await Promise.all(
            userIds.map(id => fetchUser(id))
        );
        results.successful = users;
    } catch (error) {
        console.error('Promise.all failed:', error.message);
    }
    
    return results;
}

// Promise.allSettled - מחזיר את כל התוצאות
async function loadMultipleUsersSafe(userIds) {
    const promises = userIds.map(id => fetchUser(id));
    const results = await Promise.allSettled(promises);
    
    const successful = results
        .filter(r => r.status === 'fulfilled')
        .map(r => r.value);
    
    const failed = results
        .filter(r => r.status === 'rejected')
        .map(r => r.reason);
    
    return { successful, failed };
}

// שימוש
async function example() {
    const ids = [1, 2, -1, 3]; // -1 יגרום לשגיאה
    
    const results = await loadMultipleUsersSafe(ids);
    console.log('Successful:', results.successful.length);
    console.log('Failed:', results.failed.length);
}

example();
```

### Retry Logic

```javascript
// פונקציה עם retry
async function fetchWithRetry(url, maxRetries = 3, delay = 1000) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
            console.log(`Attempt ${attempt}/${maxRetries}`);
            const response = await fetch(url);
            
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }
            
            return await response.json();
        } catch (error) {
            console.log(`Attempt ${attempt} failed:`, error.message);
            
            if (attempt === maxRetries) {
                throw new Error(`Failed after ${maxRetries} attempts: ${error.message}`);
            }
            
            // המתן לפני ניסיון נוסף
            await new Promise(resolve => setTimeout(resolve, delay));
            delay *= 2; // exponential backoff
        }
    }
}

// שימוש
async function testRetry() {
    try {
        const data = await fetchWithRetry('https://api.example.com/data');
        console.log('Data:', data);
    } catch (error) {
        console.error('All retries failed:', error.message);
    }
}
```

### Timeout Handling

```javascript
// פונקציה עם timeout
async function withTimeout(promise, timeoutMs) {
    const timeout = new Promise((_, reject) => {
        setTimeout(() => {
            reject(new Error(`Operation timed out after ${timeoutMs}ms`));
        }, timeoutMs);
    });
    
    return Promise.race([promise, timeout]);
}

// שימוש
async function fetchWithTimeout(url, timeoutMs = 5000) {
    try {
        const fetchPromise = fetch(url).then(r => r.json());
        const data = await withTimeout(fetchPromise, timeoutMs);
        return data;
    } catch (error) {
        if (error.message.includes('timed out')) {
            console.error('Request timed out');
        } else {
            console.error('Request failed:', error.message);
        }
        throw error;
    }
}

// דוגמה
async function example2() {
    try {
        const data = await fetchWithTimeout('https://api.example.com/slow', 3000);
        console.log(data);
    } catch (error) {
        console.log('Failed:', error.message);
    }
}
```

### Error Boundaries (Async)

```javascript
// Wrapper לטיפול בשגיאות אסינכרוניות
class AsyncErrorBoundary {
    constructor() {
        this.errorHandlers = [];
    }
    
    onError(handler) {
        this.errorHandlers.push(handler);
    }
    
    async execute(asyncFn) {
        try {
            return await asyncFn();
        } catch (error) {
            this.errorHandlers.forEach(handler => {
                try {
                    handler(error);
                } catch (handlerError) {
                    console.error('Error in error handler:', handlerError);
                }
            });
            throw error;
        }
    }
}

// שימוש
const boundary = new AsyncErrorBoundary();

boundary.onError(error => {
    console.log('Logging error:', error.message);
});

boundary.onError(error => {
    console.log('Sending to analytics:', error.name);
});

async function riskyOperation() {
    throw new Error('Something went wrong');
}

boundary.execute(riskyOperation)
    .catch(() => console.log('Operation failed'));
```

## Best Practices

### 1. תמיד השתמש ב-Error Objects

```javascript
// ❌ רע
throw 'Error message';
throw { error: 'Something went wrong' };

// ✅ טוב
throw new Error('Error message');
throw new ValidationError('Invalid input');
```

### 2. שגיאות ספציפיות

```javascript
// ❌ רע - שגיאה כללית מדי
function getUser(id) {
    if (!id) {
        throw new Error('Error');
    }
}

// ✅ טוב - הודעה ברורה
function getUser(id) {
    if (!id) {
        throw new Error('User ID is required');
    }
    if (typeof id !== 'number') {
        throw new TypeError('User ID must be a number');
    }
}
```

### 3. Fail Fast

```javascript
// ✅ טוב - בדוק תנאים מוקדם
function processOrder(order) {
    // בדיקות מוקדמות
    if (!order) {
        throw new Error('Order is required');
    }
    if (!order.items || order.items.length === 0) {
        throw new Error('Order must have items');
    }
    if (!order.customerId) {
        throw new Error('Customer ID is required');
    }
    
    // לוגיקה עיקרית רק אם הכל תקין
    return calculateTotal(order);
}
```

### 4. אל תבלע שגיאות

```javascript
// ❌ רע - בולע שגיאות
try {
    riskyOperation();
} catch (error) {
    // כלום - השגיאה אבודה!
}

// ❌ רע - לוג אבל לא עושה כלום
try {
    riskyOperation();
} catch (error) {
    console.log(error); // רק לוג
}

// ✅ טוב - טיפול אמיתי
try {
    riskyOperation();
} catch (error) {
    console.error('Operation failed:', error);
    // טיפול: ניסיון חוזר / fallback / הודעה למשתמש
    return fallbackValue;
}

// ✅ טוב - זריקה מחדש אחרי לוג
try {
    riskyOperation();
} catch (error) {
    console.error('Error in riskyOperation:', error);
    throw error; // זריקה מחדש
}
```

### 5. הפרד שגיאות תפעוליות מבאגים

```javascript
// שגיאות תפעוליות - צפויות (validation, network, etc.)
class OperationalError extends Error {
    constructor(message) {
        super(message);
        this.name = 'OperationalError';
        this.isOperational = true;
    }
}

// באגים - לא צפויות (קוד שגוי)
function handleError(error) {
    if (error.isOperational) {
        // שגיאה צפויה - טיפול רגיל
        console.log('Operational error:', error.message);
        // הצג הודעה למשתמש
    } else {
        // באג - לא צפוי
        console.error('Critical error:', error);
        // שלח התראה למפתחים
        // אולי צריך לעצור את התהליך
    }
}
```

### 6. Cleanup ב-Finally

```javascript
// ✅ טוב - תמיד נקה משאבים
async function processData() {
    const connection = await database.connect();
    
    try {
        const data = await connection.query('SELECT * FROM users');
        return processResults(data);
    } catch (error) {
        console.error('Query failed:', error);
        throw error;
    } finally {
        // תמיד סגור את החיבור
        await connection.close();
    }
}
```

### 7. הודעות שגיאה מועילות

```javascript
// ❌ רע
throw new Error('Invalid');

// ✅ טוב
throw new Error('Invalid email format. Expected: user@example.com');

// ✅ מצוין - עם context
throw new Error(
    `Invalid email format: "${email}". ` +
    `Expected format: user@example.com`
);
```

### 8. Error Context

```javascript
// הוסף context לשגיאות
class ContextualError extends Error {
    constructor(message, context = {}) {
        super(message);
        this.name = 'ContextualError';
        this.context = context;
        this.timestamp = new Date();
    }
}

function processPayment(userId, amount) {
    try {
        if (amount <= 0) {
            throw new ContextualError(
                'Invalid payment amount',
                {
                    userId,
                    amount,
                    action: 'processPayment'
                }
            );
        }
        // עיבוד תשלום...
    } catch (error) {
        console.error('Payment Error:', error.message);
        console.error('Context:', error.context);
        throw error;
    }
}
```

## דוגמאות מעשיות

### 1. Form Validation

```javascript
class FormValidator {
    static validateEmail(email) {
        if (!email || typeof email !== 'string') {
            throw new ValidationError('Email is required');
        }
        
        const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!regex.test(email)) {
            throw new ValidationError('Invalid email format');
        }
        
        return true;
    }
    
    static validatePassword(password) {
        if (!password || typeof password !== 'string') {
            throw new ValidationError('Password is required');
        }
        
        if (password.length < 8) {
            throw new ValidationError('Password must be at least 8 characters');
        }
        
        if (!/[A-Z]/.test(password)) {
            throw new ValidationError('Password must contain uppercase letter');
        }
        
        if (!/[a-z]/.test(password)) {
            throw new ValidationError('Password must contain lowercase letter');
        }
        
        if (!/[0-9]/.test(password)) {
            throw new ValidationError('Password must contain a number');
        }
        
        return true;
    }
    
    static validateAge(age) {
        if (typeof age !== 'number') {
            throw new TypeError('Age must be a number');
        }
        
        if (age < 0 || age > 150) {
            throw new RangeError('Age must be between 0 and 150');
        }
        
        if (age < 18) {
            throw new ValidationError('Must be 18 or older');
        }
        
        return true;
    }
    
    static validateForm(formData) {
        const errors = {};
        
        try {
            this.validateEmail(formData.email);
        } catch (error) {
            errors.email = error.message;
        }
        
        try {
            this.validatePassword(formData.password);
        } catch (error) {
            errors.password = error.message;
        }
        
        try {
            this.validateAge(formData.age);
        } catch (error) {
            errors.age = error.message;
        }
        
        if (Object.keys(errors).length > 0) {
            const error = new ValidationError('Form validation failed');
            error.errors = errors;
            throw error;
        }
        
        return true;
    }
}

// שימוש
function handleFormSubmit(formData) {
    try {
        FormValidator.validateForm(formData);
        console.log('Form is valid!');
        // שלח לשרת...
    } catch (error) {
        if (error instanceof ValidationError && error.errors) {
            console.error('Validation errors:');
            Object.entries(error.errors).forEach(([field, message]) => {
                console.log(`  ${field}: ${message}`);
            });
        } else {
            console.error('Unexpected error:', error.message);
        }
    }
}

handleFormSubmit({
    email: 'invalid-email',
    password: 'weak',
    age: 15
});
```

### 2. API Client עם Error Handling

```javascript
class APIClient {
    constructor(baseURL) {
        this.baseURL = baseURL;
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        
        try {
            const response = await fetch(url, {
                ...options,
                headers: {
                    'Content-Type': 'application/json',
                    ...options.headers
                }
            });
            
            // טיפול בשגיאות HTTP
            if (!response.ok) {
                await this.handleHTTPError(response);
            }
            
            return await response.json();
            
        } catch (error) {
            if (error instanceof NetworkError) {
                throw error;
            }
            
            // שגיאת רשת כללית
            throw new NetworkError(
                `Network request failed: ${error.message}`,
                0,
                url
            );
        }
    }
    
    async handleHTTPError(response) {
        const status = response.status;
        let errorData;
        
        try {
            errorData = await response.json();
        } catch {
            errorData = { message: response.statusText };
        }
        
        const message = errorData.message || 'Request failed';
        
        switch (status) {
            case 400:
                throw new BadRequestError(message);
            case 401:
                throw new UnauthorizedError(message);
            case 404:
                throw new NotFoundError(message);
            case 500:
                throw new InternalServerError(message);
            case 503:
                throw new ServiceUnavailableError(message);
            default:
                throw new NetworkError(message, status, response.url);
        }
    }
    
    async get(endpoint) {
        return this.request(endpoint, { method: 'GET' });
    }
    
    async post(endpoint, data) {
        return this.request(endpoint, {
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    async put(endpoint, data) {
        return this.request(endpoint, {
            method: 'PUT',
            body: JSON.stringify(data)
        });
    }
    
    async delete(endpoint) {
        return this.request(endpoint, { method: 'DELETE' });
    }
}

// שימוש
async function exampleUsage() {
    const api = new APIClient('https://api.example.com');
    
    try {
        const users = await api.get('/users');
        console.log('Users:', users);
    } catch (error) {
        if (error instanceof NotFoundError) {
            console.log('Resource not found');
        } else if (error instanceof UnauthorizedError) {
            console.log('Please log in');
            // redirect to login...
        } else if (error instanceof NetworkError) {
            console.log('Network error:', error.message);
            console.log('Status:', error.statusCode);
        } else {
            console.error('Unexpected error:', error);
        }
    }
}
```

### 3. Database Operations

```javascript
class Database {
    constructor(connectionString) {
        this.connectionString = connectionString;
        this.connection = null;
    }
    
    async connect() {
        try {
            // סימולציה של חיבור למסד נתונים
            console.log('Connecting to database...');
            this.connection = { connected: true };
            console.log('Connected successfully');
        } catch (error) {
            throw new DatabaseError(
                'Failed to connect to database',
                null,
                'CONNECTION_ERROR'
            );
        }
    }
    
    async disconnect() {
        if (this.connection) {
            console.log('Disconnecting...');
            this.connection = null;
        }
    }
    
    async query(sql, params = []) {
        if (!this.connection) {
            throw new DatabaseError(
                'Not connected to database',
                sql,
                'NO_CONNECTION'
            );
        }
        
        try {
            console.log('Executing query:', sql);
            // סימולציה של query
            return [{ id: 1, name: 'John' }];
        } catch (error) {
            throw new DatabaseError(
                `Query failed: ${error.message}`,
                sql,
                'QUERY_ERROR'
            );
        }
    }
    
    async transaction(operations) {
        try {
            await this.query('BEGIN TRANSACTION');
            
            const results = [];
            for (const operation of operations) {
                const result = await operation();
                results.push(result);
            }
            
            await this.query('COMMIT');
            return results;
            
        } catch (error) {
            await this.query('ROLLBACK');
            throw new DatabaseError(
                `Transaction failed: ${error.message}`,
                'TRANSACTION',
                'ROLLBACK'
            );
        }
    }
}

// שימוש
async function databaseExample() {
    const db = new Database('connection-string');
    
    try {
        await db.connect();
        
        const users = await db.query('SELECT * FROM users');
        console.log('Users:', users);
        
        // טרנזקציה
        await db.transaction([
            () => db.query('INSERT INTO users VALUES (?, ?)', ['John', 'john@example.com']),
            () => db.query('UPDATE accounts SET balance = balance - 100 WHERE id = 1'),
            () => db.query('UPDATE accounts SET balance = balance + 100 WHERE id = 2')
        ]);
        
        console.log('Transaction completed');
        
    } catch (error) {
        if (error instanceof DatabaseError) {
            console.error('Database error:', error.message);
            console.error('Code:', error.code);
            if (error.query) {
                console.error('Query:', error.query);
            }
        } else {
            console.error('Unexpected error:', error);
        }
    } finally {
        await db.disconnect();
    }
}

databaseExample();
```

### 4. File Operations

```javascript
class FileManager {
    static async readFile(path) {
        try {
            // סימולציה של קריאת קובץ
            if (!path) {
                throw new Error('File path is required');
            }
            
            if (path.includes('not-found')) {
                const error = new Error(`File not found: ${path}`);
                error.code = 'ENOENT';
                throw error;
            }
            
            if (path.includes('forbidden')) {
                const error = new Error(`Permission denied: ${path}`);
                error.code = 'EACCES';
                throw error;
            }
            
            return 'File content';
            
        } catch (error) {
            if (error.code === 'ENOENT') {
                throw new NotFoundError(`File not found: ${path}`);
            } else if (error.code === 'EACCES') {
                throw new Error(`Permission denied: ${path}`);
            } else {
                throw new Error(`Failed to read file: ${error.message}`);
            }
        }
    }
    
    static async writeFile(path, content) {
        try {
            if (!path || !content) {
                throw new ValidationError('Path and content are required');
            }
            
            console.log(`Writing to ${path}:`, content);
            return true;
            
        } catch (error) {
            throw new Error(`Failed to write file: ${error.message}`);
        }
    }
    
    static async safeFileOperation(operation, fallback = null) {
        try {
            return await operation();
        } catch (error) {
            console.warn('File operation failed:', error.message);
            return fallback;
        }
    }
}

// שימוש
async function fileExample() {
    // קריאת קובץ עם טיפול בשגיאות
    try {
        const content = await FileManager.readFile('data.txt');
        console.log('Content:', content);
    } catch (error) {
        if (error instanceof NotFoundError) {
            console.log('File does not exist, creating...');
            await FileManager.writeFile('data.txt', 'Default content');
        } else {
            console.error('Error:', error.message);
        }
    }
    
    // פעולה בטוחה עם fallback
    const content = await FileManager.safeFileOperation(
        () => FileManager.readFile('config.json'),
        { default: 'config' }
    );
    console.log('Config:', content);
}

fileExample();
```

## סיכום

### כללי זהב לטיפול בשגיאות:

1. ✅ **תמיד השתמש ב-Error objects** - לא strings או מספרים
2. ✅ **הודעות שגיאה ברורות** - תאר מה השתבש ואיך לתקן
3. ✅ **Fail fast** - בדוק תנאים מוקדם
4. ✅ **אל תבלע שגיאות** - טפל או זרוק מחדש
5. ✅ **נקה משאבים ב-finally** - תמיד
6. ✅ **צור Custom Errors** - לשגיאות ספציפיות לאפליקציה
7. ✅ **הפרד שגיאות תפעוליות מבאגים** - טיפול שונה
8. ✅ **הוסף context** - מידע שעוזר ב-debugging

### מתי להשתמש במה?

| מצב | פתרון |
|-----|--------|
| **Synchronous code** | try-catch |
| **Promises** | .catch() או try-catch עם await |
| **Async/Await** | try-catch |
| **Cleanup** | finally |
| **Validation** | throw new Error |
| **Custom logic** | Custom Error classes |
| **Multiple async** | Promise.allSettled |

### תרגילים מומלצים:

1. **Form Validator**: בנה מערכת validation עם שגיאות מותאמות
2. **API Client**: צור client עם טיפול מלא בשגיאות HTTP
3. **File System**: מימוש פעולות קובץ עם error handling
4. **Database**: צור wrapper למסד נתונים עם transactions
5. **Retry Logic**: מימוש retry עם exponential backoff
6. **Error Logger**: בנה מערכת logging לשגיאות

בהצלחה! 🚀
