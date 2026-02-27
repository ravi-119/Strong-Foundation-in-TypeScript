
# Literal Types in TypeScript

Literal types allow you to specify exact values that a variable can have, rather than just a general type.

## String Literals

```typescript
let direction: "up" | "down" | "left" | "right";
direction = "up"; // ✓ Valid
direction = "forward"; // ✗ Error
```

## Number Literals

```typescript
let statusCode: 200 | 404 | 500;
statusCode = 200; // ✓ Valid
statusCode = 201; // ✗ Error
```

## Boolean Literals

```typescript
let isTrue: true;
isTrue = true; // ✓ Valid
isTrue = false; // ✗ Error
```

## Union Types with Literals

```typescript
type Response = {
    status: "success" | "error";
    data?: any;
};

const response: Response = {
    status: "success",
    data: { id: 1 }
};
```

## Benefits

- **Type Safety**: Restricts values to specific options
- **Autocomplete**: IDEs provide better suggestions
- **Self-Documenting**: Code intent is clearer


## Tuples

Tuples are arrays with fixed length and specific types at each position.

```typescript
let tuple: [string, number];
tuple = ["hello", 42]; // ✓ Valid
tuple = [42, "hello"]; // ✗ Error

let user: [string, number, boolean] = ["john", 30, true];
```

### Optional Elements

```typescript
let optional: [string, number?];
optional = ["hello"]; // ✓ Valid
optional = ["hello", 42]; // ✓ Valid
```

### Rest Elements

```typescript
let rest: [string, ...number[]];
rest = ["id", 1, 2, 3]; // ✓ Valid
```


## Tuples Destructuring in TypeScript

TypeScript supports destructuring tuples, allowing you to unpack values from a tuple into distinct variables. This is useful for handling fixed-size arrays with known types.

### Example:

```typescript
// Define a tuple
let person: [string, number] = ["Alice", 30];

// Destructure the tuple
let [name, age] = person;

console.log(name); // Output: Alice
console.log(age);  // Output: 30
```

You can also use rest parameters for remaining elements:

```typescript
let coordinates: [number, number, number] = [10, 20, 30];
let [x, y, ...rest] = coordinates;

console.log(x);    // Output: 10
console.log(y);    // Output: 20
console.log(rest); // Output: [30]
```

Destructuring works with function returns as well:

```typescript
function getPerson(): [string, number] {
    return ["Bob", 25];
}

let [name, age] = getPerson();
console.log(name, age); // Output: Bob 25
```



## Enums

Enums (short for *enumerations*) are a way to give more friendly names to sets of numeric or string values. They create a new type that can be used just like other types, and the values are accessible both by name and by value.

### Numeric Enums

```typescript
enum Direction {
    Up,
    Down,
    Left,
    Right
}

let dir: Direction = Direction.Up;
console.log(dir); // 0

// Reverse mapping
console.log(Direction[0]); // "Up"
```

By default, enums start at 0 and increment by 1. You can provide custom values:

```typescript
enum Status {
    Success = 1,
    Warning = 5,
    Error = 10
}
```

### String Enums

```typescript
enum Color {
    Red = "RED",
    Green = "GREEN",
    Blue = "BLUE"
}

let c: Color = Color.Green;
console.log(c); // "GREEN"
```

### Heterogeneous Enums (not recommended)

Although possible, mixing string and number values is uncommon:

```typescript
enum Mixed {
    No = 0,
    Yes = "YES"
}
```

### Using Enums in Interfaces

```typescript
interface Point {
    x: number;
    y: number;
    orientation: Direction;
}

const pt: Point = { x: 10, y: 20, orientation: Direction.Left };
```

### Benefits of Enums

- **Readability**: Use human-friendly names instead of magic numbers.
- **Maintainability**: Centralize a set of related constants.
- **Safety**: Only allowed values can be assigned.




## Class Properties Annotations

Class properties in TypeScript require explicit type annotations. This ensures type safety and allows the compiler to catch errors at compile time.

### Basic Property Annotations

```typescript
class Person {
    name: string;
    age: number;
    email: string;

    constructor(name: string, age: number, email: string) {
        this.name = name;
        this.age = age;
        this.email = email;
    }
}

const person = new Person("John", 30, "john@example.com");
```

### Optional Properties

```typescript
class User {
    name: string;
    phone?: string;

    constructor(name: string, phone?: string) {
        this.name = name;
        this.phone = phone;
    }
}
```

### Readonly Properties

```typescript
class Config {
    readonly apiUrl: string;
    readonly version: string = "1.0";

    constructor(apiUrl: string) {
        this.apiUrl = apiUrl;
    }
}
```

### Access Modifiers

```typescript
class Employee {
    public name: string;
    private salary: number;
    protected department: string;

    constructor(name: string, salary: number, department: string) {
        this.name = name;
        this.salary = salary;
        this.department = department;
    }
}
```

### Parameter Properties

```typescript
class Product {
    constructor(
        public id: number,
        public title: string,
        private price: number
    ) {}
}

const product = new Product(1, "Laptop", 999);
console.log(product.title); // "Laptop"
```

## Readonly Keyword

The `readonly` keyword in TypeScript prevents properties from being modified after initialization. It creates immutable properties at the class level.

### Basic Usage

```typescript
class User {
    readonly id: number;
    name: string;

    constructor(id: number, name: string) {
        this.id = id;        // ✓ Can assign in constructor
        this.name = name;
    }
}

const user = new User(1, "Alice");
user.name = "Bob";            // ✓ Valid
user.id = 2;                  // ✗ Error: Cannot assign to readonly
```

### With Initial Values

```typescript
class Config {
    readonly maxRetries: number = 3;
    readonly timeout: number = 5000;
}
```

### In Parameter Properties

```typescript
class Document {
    constructor(
        readonly id: string,
        readonly title: string,
        public content: string
    ) {}
}

const doc = new Document("1", "Report", "Draft content");
doc.content = "Updated";      // ✓ Valid
doc.title = "New Report";     // ✗ Error: readonly
```

### With Arrays and Objects

```typescript
class Settings {
    readonly permissions: string[] = ["read"];
}

const settings = new Settings();
settings.permissions.push("write");  // ✓ Valid (mutating, not reassigning)
settings.permissions = [];            // ✗ Error: Cannot reassign
```



## Access Modifiers

Access modifiers control the visibility of class members. TypeScript provides three main access modifiers:

### Public

Members are accessible from anywhere (default if not specified):

```typescript
class Animal {
    public name: string;

    public constructor(name: string) {
        this.name = name;
    }

    public speak() {
        console.log(`${this.name} makes a sound`);
    }
}

const dog = new Animal("Dog");
console.log(dog.name); // ✓ Valid
dog.speak(); // ✓ Valid
```

### Private

Members are only accessible within the class:

```typescript
class BankAccount {
    private balance: number;

    constructor(initialBalance: number) {
        this.balance = initialBalance;
    }

    private logTransaction(amount: number) {
        console.log(`Transaction: ${amount}`);
    }

    public withdraw(amount: number) {
        if (amount <= this.balance) {
            this.balance -= amount;
            this.logTransaction(amount);
        }
    }
}

const account = new BankAccount(1000);
account.withdraw(100); // ✓ Valid
console.log(account.balance); // ✗ Error: Private property
```

### Protected

Members are accessible within the class and its subclasses:

```typescript
class Vehicle {
    protected speed: number = 0;

    protected accelerate() {
        this.speed += 10;
    }
}

class Car extends Vehicle {
    public drive() {
        this.accelerate(); // ✓ Valid (accessible in subclass)
        console.log(`Driving at ${this.speed} km/h`);
    }
}

const car = new Car();
car.drive(); // ✓ Valid
car.speed = 50; // ✗ Error: Protected property
```





## Getters & Setters

Getters and setters allow you to define custom logic when accessing or modifying class properties. They provide encapsulation and validation while maintaining a property-like syntax.

### Basic Getter and Setter

```typescript
class Person {
    private _age: number;

    constructor(age: number) {
        this._age = age;
    }

    get age(): number {
        return this._age;
    }

    set age(value: number) {
        if (value < 0) {
            console.log("Age cannot be negative");
            return;
        }
        this._age = value;
    }
}

const person = new Person(30);
console.log(person.age); // 30 (calls getter)
person.age = 25; // ✓ Valid (calls setter)
person.age = -5; // ✗ Error message displayed
```

### Getter Only

```typescript
class Circle {
    private _radius: number;

    constructor(radius: number) {
        this._radius = radius;
    }

    get area(): number {
        return Math.PI * this._radius ** 2;
    }
}

const circle = new Circle(5);
console.log(circle.area); // ✓ Valid (read-only)
circle.area = 100; // ✗ Error: Cannot set read-only property
```

### Validation in Setters

```typescript
class User {
    private _email: string;

    set email(value: string) {
        if (!value.includes("@")) {
            throw new Error("Invalid email format");
        }
        this._email = value;
    }

    get email(): string {
        return this._email;
    }
}

const user = new User();
user.email = "test@example.com"; // ✓ Valid
user.email = "invalid-email"; // ✗ Error thrown
```

### Benefits

- **Encapsulation**: Hide internal implementation details
- **Validation**: Enforce rules when setting values
- **Computed Properties**: Calculate values on the fly with getters
- **Clean Syntax**: Access properties without method call syntax



## Interfaces

Interfaces define contracts for the structure of objects. They specify what properties and methods an object must have, enabling type checking and documentation.

### Basic Interface

```typescript
interface User {
    name: string;
    age: number;
    email: string;
}

const user: User = {
    name: "Alice",
    age: 30,
    email: "alice@example.com"
};
```

### Optional Properties

```typescript
interface Product {
    id: number;
    title: string;
    description?: string;
}

const product: Product = { id: 1, title: "Laptop" }; // ✓ Valid
```

### Readonly Properties

```typescript
interface Config {
    readonly apiUrl: string;
    readonly port: number;
}
```

### Methods in Interfaces

```typescript
interface Animal {
    name: string;
    speak(): void;
    move(distance: number): void;
}

class Dog implements Animal {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    speak() {
        console.log("Woof!");
    }

    move(distance: number) {
        console.log(`Moved ${distance} meters`);
    }
}
```

### Extending Interfaces

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee extends Person {
    employeeId: number;
    department: string;
}

const emp: Employee = {
    name: "Bob",
    age: 28,
    employeeId: 123,
    department: "Engineering"
};
```

### Interface Inheritance (Multiple)

```typescript
interface HasName {
    name: string;
}

interface HasAge {
    age: number;
}

interface Person extends HasName, HasAge {
    email: string;
}
```

### Class Implementing Multiple Interfaces

```typescript
interface Printable {
    print(): void;
}

interface Saveable {
    save(): void;
}

class Document implements Printable, Saveable {
    print() {
        console.log("Printing...");
    }

    save() {
        console.log("Saving...");
    }
}
```

## Declaration Merging

Declaration merging allows you to combine multiple declarations with the same name into a single definition. TypeScript merges the declarations together, combining their properties and methods.

### Interface Merging

```typescript
interface User {
    name: string;
}

interface User {
    age: number;
}

// Merged interface has both properties
const user: User = {
    name: "Alice",
    age: 30
};
```

### Namespace Merging

```typescript
namespace Utils {
    export function log(msg: string) {
        console.log(msg);
    }
}

namespace Utils {
    export function error(msg: string) {
        console.error(msg);
    }
}

Utils.log("Info");     // ✓ Valid
Utils.error("Error");  // ✓ Valid
```

### Class and Namespace Merging

```typescript
class Animal {
    name: string = "Animal";
}

namespace Animal {
    export const species = "Unknown";
}

console.log(Animal.species); // "Unknown"
```

### Adding Methods to Existing Types

```typescript
interface Array<T> {
    isEmpty(): boolean;
}

Array.prototype.isEmpty = function() {
    return this.length === 0;
};

const arr = [1, 2, 3];
console.log(arr.isEmpty()); // false
```

### Merging with Generics

```typescript
interface Repository<T> {
    find(id: number): T;
}

interface Repository<T> {
    save(item: T): void;
}

const userRepo: Repository<User> = {
    find(id: number) { return {} as User; },
    save(item: User) { }
};
```

## Generics

Generics allow you to write reusable code that works with multiple types while maintaining type safety. They enable you to create functions, classes, and interfaces that can operate on different data types.

### Generic Functions

```typescript
function identity<T>(value: T): T {
    return value;
}

console.log(identity<string>("hello")); // "hello"
console.log(identity<number>(42)); // 42
console.log(identity(true)); // true (type inferred)
```

### Multiple Type Parameters

```typescript
function swap<T, U>(a: T, b: U): [U, T] {
    return [b, a];
}

const result = swap<string, number>("hello", 42);
console.log(result); // [42, "hello"]
```

### Generic Classes

```typescript
class Box<T> {
    private value: T;

    constructor(value: T) {
        this.value = value;
    }

    getValue(): T {
        return this.value;
    }

    setValue(value: T): void {
        this.value = value;
    }
}

const stringBox = new Box<string>("TypeScript");
const numberBox = new Box<number>(100);
```

### Generic Interfaces

```typescript
interface Repository<T> {
    add(item: T): void;
    remove(item: T): void;
    getAll(): T[];
}

class UserRepository implements Repository<User> {
    private users: User[] = [];

    add(item: User): void {
        this.users.push(item);
    }

    remove(item: User): void {
        this.users = this.users.filter(u => u !== item);
    }

    getAll(): User[] {
        return this.users;
    }
}
```

### Type Constraints

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const person = { name: "Alice", age: 30 };
const name = getProperty(person, "name"); // ✓ Valid
// getProperty(person, "email"); // ✗ Error: "email" not in person
```

### Extending Types in Generics

```typescript
interface Named {
    name: string;
}

function printName<T extends Named>(obj: T): void {
    console.log(obj.name);
}

printName({ name: "Bob", age: 30 }); // ✓ Valid
// printName({ age: 30 }); // ✗ Error: missing name
```

### Default Type Parameters

```typescript
interface Container<T = string> {
    value: T;
}

const stringContainer: Container = { value: "hello" };
const numberContainer: Container<number> = { value: 42 };
```

### Generic Utility Types

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}

type ReadonlyUser = Readonly<User>;
type PartialUser = Partial<User>;
type UserPreview = Pick<User, "name" | "email">;
type UserUpdate = Omit<User, "id">;
```
