# Type Narrowing in TypeScript

Type narrowing is a technique to refine a variable's type within a specific scope, allowing TypeScript to understand the more specific type you're working with.

## Common Type Narrowing Techniques

### 1. **typeof Guard**
```typescript
function printLength(str: string | number) {
    if (typeof str === "string") {
        console.log(str.length); // str is string
    } else {
        console.log(str.toFixed(2)); // str is number
    }
}
```

### 2. **instanceof Guard**
```typescript
class Dog {
    bark() { }
}

function isDog(pet: Dog | string): pet is Dog {
    return pet instanceof Dog;
}
```

### 3. **Truthiness Check**
```typescript
function printLength(str: string | null) {
    if (str) {
        console.log(str.length); // str is string
    }
}
```

### 4. **Equality Check**
```typescript
function compare(x: string | number, y: boolean | string) {
    if (x === y) {
        // x and y must be string
    }
}
```

### 5. **Intersection Type**
```typescript
interface Named {
    name: string;
}

interface Aged {
    age: number;
}

type Person = Named & Aged;

function greet(person: Person) {
    console.log(`${person.name} is ${person.age}`); // person has both properties
}
```

### 6. **Control Flow Analysis**
```typescript
function process(value: string | undefined) {
    if (!value) return;
    console.log(value.toUpperCase()); // value is string
}
```

## Benefits
- Prevents runtime errors
- Improves code readability
- Enables better IDE autocomplete

## TypeScript Configuration (tsconfig.json)

`tsconfig.json` is a configuration file that specifies compiler options and project settings for TypeScript.

### File Structure
The `tsconfig.json` file should be placed at the root of your TypeScript project. It contains three main sections: `compilerOptions`, `include`, and `exclude`.

### Basic Structure
```typescript
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "commonjs",
        "lib": ["ES2020"],
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules", "dist", "**/*.spec.ts"]
}
```

### Include Option
Specifies which files to include in compilation:
```typescript
"include": [
    "src/**/*",      // All files in src directory
    "tests/**/*.ts"  // TypeScript files in tests
]
```

### Exclude Option
Specifies which files to exclude from compilation:
```typescript
"exclude": [
    "node_modules",     // Dependencies
    "dist",             // Output directory
    "**/*.spec.ts",     // Test files
    "**/*.d.ts"         // Declaration files
]
```

### Key Compiler Options
- `target`: ECMAScript version for output
- `module`: Module system (commonjs, es6, etc.)
- `strict`: Enable strict type checking
- `rootDir`: Root directory of source files
- `outDir`: Output directory for compiled files
- `declaration`: Generate `.d.ts` files



## Detailed Compiler Options

### `target`
Specifies the ECMAScript version for the compiled output. Common values: `ES5`, `ES2015`, `ES2020`, `ESNext`.

### `module`
Defines the module system for the output code. Common values: `commonjs`, `es6`, `esnext`, `amd`, `umd`.

### `strict`
Enables all strict type checking options. When true, enforces stricter analysis to catch more potential errors at compile time.

### `rootDir`
Specifies the root directory of your source files. TypeScript uses this to determine the structure of output files.

### `outDir`
Specifies the output directory where compiled JavaScript files will be written.

### `declaration`
When enabled, generates corresponding `.d.ts` declaration files alongside the JavaScript output. Declaration files provide type information for consumers of your library.






## Axios in TypeScript

Axios is a popular HTTP client library for making requests in TypeScript and JavaScript applications.

### Installation
```bash
npm install axios
```

### Basic GET Request
```typescript
import axios from 'axios';

async function fetchUser() {
    try {
        const response = await axios.get('https://api.example.com/user/1');
        console.log(response.data);
    } catch (error) {
        console.error('Error fetching user:', error);
    }
}
```

### POST Request with Data
```typescript
interface User {
    name: string;
    email: string;
}

async function createUser(user: User) {
    try {
        const response = await axios.post('https://api.example.com/users', user);
        console.log('User created:', response.data);
    } catch (error) {
        console.error('Error creating user:', error);
    }
}
```

### Request Configuration
```typescript
const config = {
    method: 'GET',
    url: 'https://api.example.com/data',
    headers: {
        'Authorization': 'Bearer token123'
    },
    timeout: 5000
};

axios(config).then(response => console.log(response.data));
```

### Interceptors
```typescript
axios.interceptors.request.use(config => {
    config.headers['Authorization'] = 'Bearer token';
    return config;
});

axios.interceptors.response.use(
    response => response,
    error => Promise.reject(error)
);
```