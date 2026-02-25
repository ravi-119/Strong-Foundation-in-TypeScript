# Annotations in TypeScript

## Overview
- **Definition:** Type annotations are explicit types attached to variables, parameters, return values, and properties so the compiler can check correctness and editors can provide better tooling.
- **Inference:** TypeScript infers many types automatically; add annotations when they improve clarity or define public API boundaries.

## Basics
- Variable: `let count: number = 0;`
- Function: `function sum(a: number, b: number): number { return a + b }`
- Object / Interface:

```ts
interface User { id: number; name: string; active?: boolean }
const u: User = { id: 1, name: 'Ravi' }
```
- Type alias / Union: `type ID = string | number; let uid: ID = 'abc'`
- Tuple: `const pair: [number, string] = [1, 'one']`

## Advanced annotations
- Generics:

```ts
function identity<T>(arg: T): T { return arg }
```
- Mapped types:

```ts
type ReadOnly<T> = { readonly [K in keyof T]: T[K] }
```
- Index signatures: `type Dict<T> = { [key: string]: T }`
- Conditional types:

```ts
type IsString<T> = T extends string ? true : false
```
- Intersection / Union types: `type A = X & Y; type B = X | Y`

## Type assertions & operators
- Assertion: `const el = document.getElementById('id') as HTMLDivElement`
- Non-null assertion: `value!.prop` (use sparingly)
- Prefer `unknown` over `any` when possible — `unknown` forces safety checks.

## JSDoc & Decorators
- JSDoc: add `/** @type {number} */` in JavaScript files to give TypeScript type hints.
- Decorators: experimental feature for class/method/property metadata. Enable with `experimentalDecorators` in `tsconfig.json`.

## Best practices (quick)
- **Annotate public APIs:** exports, library boundaries, component props.
- **Prefer inference for locals:** avoid redundant annotations like `let x: number = 1`.
- **Use `unknown` over `any`:** forces explicit type checks.
- **Keep types small and composable:** prefer aliases, interfaces, and generics.
- **Annotate when the compiler can't infer:** DOM lookups, external JSON, and runtime data.

---

If you'd like, I can add a short `ANNOTATIONS.md` cheatsheet in the repo or show React-specific prop and event annotation examples.

## Type Inference

- **Definition:** Type inference is TypeScript's ability to automatically determine the type of an expression from its usage or initializer when you don't provide an explicit annotation.
- **Examples:**

```ts
let x = 3; // x: number (inferred)
function greet() { return 'hi' } // greet(): string (inferred)
const handlers: ((e: Event) => void)[] = [e => e.preventDefault()]; // parameter typed contextually
```
- **Benefits:** reduces boilerplate, keeps code concise, and preserves editor tooling and safety.
- **When to annotate anyway:** public APIs, external JSON/DOM lookups, complex unions or overloads, and to document intent.
- **Caveats:** inference can widen (e.g., `[]` → `any[]`) or be too broad; prefer explicit types at boundaries and use `unknown` instead of `any` for untrusted input.


## The `any` Type

- **Definition:** `any` disables compile-time type checking for a value; values typed `any` can be assigned to or from any other type.
- **Example:**

```ts
let raw: any = JSON.parse(someInput);
raw.foo.bar; // no compile-time errors
```
- **Pitfalls:** using `any` loses type safety, hides bugs, and removes editor completions and refactor-safety.
- **Prefer:** `unknown` for safer unknown inputs, explicit interfaces/types, or writing type guards to narrow values.
- **When to use:** short-term during migration, prototyping, or when integrating untyped third-party code — prefer adding proper typings or declaration files instead.
- **Quick tips:** enable `noImplicitAny` in `tsconfig.json` and avoid `as any` except where absolutely necessary.


## Default Parameters

- **Syntax:** provide a default after the parameter declaration: `function f(a: number = 1, b: string = 'x') {}`
- **Examples:**

```ts
function multiply(x: number, y: number = 1): number {
	return x * y;
}

const greet = (name: string = 'Guest'): string => `Hello, ${name}`;
```
- **Destructured params with defaults:**

```ts
function createUser({ name = 'Anonymous', active = true }: { name?: string; active?: boolean } = {}) {
	return { name, active };
}
```
- **Notes:** defaults apply when an argument is `undefined` (not `null`); they work with typed parameters; prefer simple expressions for defaults to avoid hidden side effects.

## Return Type Annotations

- **Definition:** an explicit return type declares the type a function will return (e.g., `: number`, `: void`, `: Promise<T>`). It documents intent and enforces the API surface.
- **Examples:**

```ts
function sum(a: number, b: number): number {
	return a + b;
}

const greet = (name = 'Guest'): string => `Hello, ${name}`;

function log(message: string): void {
	console.log(message);
}

async function fetchUser(id: string): Promise<User> {
	const res = await fetch(`/users/${id}`);
	return res.json();
}

function fail(msg: string): never {
	throw new Error(msg);
}
```
- **Best practices:** annotate exported/public functions and async Promise returns; allow inference for simple local helpers; avoid `any` as a return type.
- **Note:** TypeScript will infer return types from function bodies, but explicit return annotations improve readability and catch unintended widening at API boundaries.


## The `void` Type

- **Definition:** `void` represents the absence of any meaningful value — it's commonly used as the return type for functions that don't return a value.
- **Examples:**

```ts
function log(message: string): void {
	console.log(message);
}

const cb = (n: number): void => { /* side-effect only */ };
```
- **Variables typed `void`:** rarely useful. In `strict` mode a `void` value is effectively `undefined` and cannot hold useful data:

```ts
let v: void = undefined; // allowed in most TS configs
v = 1; // error
```
- **`void` vs `never`:** use `void` for functions that return normally with no value; use `never` for functions that never return (throw or infinite loop).
- **Callbacks:** `void` signals a callback is used for side-effects and its return value should be ignored by callers.

## The `never` Type

- **Definition:** `never` represents values that never occur. Use it for functions that never return (they throw or loop forever) or to mark unreachable code paths.
- **Examples:**

```ts
function fail(msg: string): never {
	throw new Error(msg);
}

function loopForever(): never {
	while (true) {}
}
```
- **Exhaustiveness checks:** commonly used in `switch`/`if` exhaustiveness guards to ensure all union cases are handled:

```ts
type Shape = { kind: 'circle'; r: number } | { kind: 'square'; s: number };
function area(s: Shape): number {
	switch (s.kind) {
		case 'circle': return Math.PI * s.r * s.r;
		case 'square': return s.s * s.s;
		default:
			const _exhaust: never = s; // compile-time error if a case is missing
			return _exhaust;
	}
}
```
- **Usage notes:**
	- `never` is the bottom type — it can be assigned to every type, but no type (except `never`) is assignable to `never`.
	- Use `never` to catch logic mistakes and to document truly impossible code paths.
	- `never` is different from `void`: `void` means "no useful value" for returned results, while `never` means "no return at all".

## Arrays in TypeScript

- **Basic syntax:** `T[]` or `Array<T>` for arrays of `T`.

```ts
const nums: number[] = [1, 2, 3];
const strs: Array<string> = ['a', 'b'];
```
- **Tuples:** fixed-length arrays with specific types per index: `const pair: [number, string] = [1, 'one']`.
- **Readonly arrays/tuples:** `readonly T[]` or `readonly [T, U]` to prevent mutation.
- **Array methods typing:** methods like `map`, `filter`, `reduce` preserve or narrow types via generics.

```ts
const doubled = nums.map(n => n * 2); // number[]
const names = ['Alice', 'Bob'] as const; // readonly ['Alice', 'Bob'] (literal types)
```
- **Heterogeneous arrays:** prefer tuples or unions, e.g., `(string | number)[]`.
- **Empty arrays:** annotate when empty: `const list: string[] = []` to avoid `any[]`.
- **Multidimensional arrays:** `number[][]` for arrays of arrays, or `Array<Array<T>>`.
- **Best practices:** prefer `T[]` for simplicity; use tuples for fixed structures; annotate empty arrays; use `readonly` when mutation isn't needed.
examples:
```ts
const matrix: number[][] = [[1, 2], [3, 4]];
const grid: Array<Array<string>> = [['a', 'b'], ['c', 'd']];
const tensor: number[][][] = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]];
```



## Objects in TypeScript

- **Basic syntax:** use `interface`, `type`, or inline object type annotations to define object shapes.

```ts
interface User { id: number; name: string; email: string }
type Product = { id: number; title: string; price: number }
const obj: { x: number; y: number } = { x: 1, y: 2 };
```

- **Optional properties:** use `?` to mark properties as optional.

```ts
interface Config { host: string; port?: number; timeout?: number }
const cfg: Config = { host: 'localhost' }; // valid
```

- **Readonly properties:** prevent mutation with `readonly`.

```ts
interface Point { readonly x: number; readonly y: number }
const p: Point = { x: 1, y: 2 };
p.x = 5; // error
```

- **Index signatures:** define flexible key-value pairs.

```ts
type Dict<T> = { [key: string]: T };
const ages: Dict<number> = { alice: 30, bob: 25 };
```

- **Nested objects:** compose types with other objects or interfaces.

```ts
interface Address { street: string; city: string }
interface Person { name: string; address: Address }
```

- **Object methods:** annotate method signatures within interfaces.

```ts
interface Logger { log(msg: string): void; error(msg: string): never }
```

- **Object spread & merging:** combine types with intersection (`&`) or destructuring.

```ts
type X = { a: number }; type Y = { b: string };
type XY = X & Y; // { a: number; b: string }
const obj: XY = { a: 1, b: 'hi' };
```

- **Best practices:** prefer `interface` for public APIs; use `type` for unions and complex shapes; mark `readonly` when mutation isn't needed; annotate empty objects to avoid inference issues.


## Type Aliases in TypeScript

- **Definition:** a type alias is a name for any type, declared with the `type` keyword. It allows you to reuse complex type definitions and improve code readability.

```ts
type ID = string | number;
type User = { id: ID; name: string; email: string };
```

- **Basic syntax:** `type Name = TypeDefinition;`

```ts
type Age = number;
type Status = 'active' | 'inactive' | 'pending';
type Callback = (data: string) => void;
```

- **Union types:** combine multiple types with `|`.

```ts
type Result = string | number | boolean;
type Response = { data: any } | { error: string };
```

- **Intersection types:** combine types with `&` to merge properties.

```ts
type A = { x: number }; type B = { y: string };
type AB = A & B; // { x: number; y: string }
```

- **Generic aliases:** parameterize types for reusability.

```ts
type Container<T> = { value: T; isEmpty(): boolean };
type Pair<T, U> = [T, U];
```

- **Tuple and array aliases:** simplify array type declarations.

```ts
type Coordinates = [number, number];
type StringList = string[];
```

- **Type aliases vs. interfaces:**
    - Aliases support unions, tuples, and primitives; interfaces are for object shapes only.
    - Interfaces are extendable and mergeable; aliases are not.
    - Use `interface` for public APIs; use `type` for complex or union types.

```ts
interface Shape { area(): number } // extendable
type Shape = Circle | Square; // union of types
```

- **Best practices:** name aliases clearly and descriptively; prefer `interface` for object contracts; use aliases for unions, tuples, and function types; avoid overly nested or circular aliases.




## Optional Properties

- **Definition:** optional properties can be omitted when creating an object. Mark them with `?` after the property name.

```ts
interface User { id: number; name: string; email?: string }
const u1: User = { id: 1, name: 'Ravi' }; // valid, email omitted
const u2: User = { id: 2, name: 'Jane', email: 'jane@example.com' }; // valid
```

- **Accessing optional properties:** values are `T | undefined`; check before use.

```ts
interface Config { timeout?: number }
const cfg: Config = {};
console.log(cfg.timeout); // undefined
if (cfg.timeout) { /* ... */ }
```

- **Optional parameters in functions:** use `?` after the parameter name.

```ts
function greet(name: string, greeting?: string): string {
    return `${greeting || 'Hello'}, ${name}`;
}
greet('Ravi'); // valid
greet('Ravi', 'Hi'); // valid
```

- **Optional vs. defaults:** optional allows `undefined`; defaults provide a fallback.

```ts
function f(a?: number) { }; f(); f(undefined); // both valid
function g(a: number = 1) { }; g(); // valid, a is 1
```

- **Best practices:** use optional for truly optional fields; prefer defaults for function parameters; avoid excessive optionals — consider breaking into separate types.



## Intersection Types

- **Definition:** intersection types combine multiple types using `&` to create a new type with all properties and methods from each type.

```ts
type A = { x: number };
type B = { y: string };
type AB = A & B; // { x: number; y: string }

const obj: AB = { x: 1, y: 'hello' };
```

- **With interfaces:**

```ts
interface Admin { role: 'admin'; permissions: string[] }
interface User { id: number; name: string }
type AdminUser = Admin & User;

const admin: AdminUser = { 
    id: 1, 
    name: 'Ravi', 
    role: 'admin', 
    permissions: ['read', 'write'] 
};
```

- **Function types:** combine function signatures.

```ts
type LogFn = (msg: string) => void;
type TimeFn = (ms: number) => void;
type LogAndTime = LogFn & TimeFn; // requires both signatures
```

- **Conflicting properties:** if types have conflicting property types, the result is `never`.

```ts
type X = { prop: string };
type Y = { prop: number };
type XY = X & Y; // prop: never (impossible to satisfy both)
```

- **Best practices:** use intersections to compose types and extend existing types; prefer them over repeating properties; watch for conflicts and `never` results.







## Union Types

- **Definition:** union types combine multiple types using `|` to create a type that can be any one of the specified types.

```ts
type ID = string | number;
let userId: ID = 'abc'; // valid
userId = 123; // valid
userId = true; // error
```

- **With literals:** create discriminated unions for safer type narrowing.

```ts
type Response = { status: 'success'; data: any } | { status: 'error'; message: string };
const res: Response = { status: 'success', data: { id: 1 } };
```

- **Type narrowing:** use `typeof`, `instanceof`, or property checks to narrow unions.

```ts
function process(value: string | number): void {
    if (typeof value === 'string') {
        console.log(value.toUpperCase()); // string method
    } else {
        console.log(value.toFixed(2)); // number method
    }
}
```

- **Discriminated unions:** use a common property (discriminator) to distinguish union members.

```ts
type Shape = 
    | { kind: 'circle'; radius: number }
    | { kind: 'square'; side: number };

function area(shape: Shape): number {
    switch (shape.kind) {
        case 'circle': return Math.PI * shape.radius ** 2;
        case 'square': return shape.side ** 2;
    }
}
```

- **Union vs. intersection:**
    - Union (`|`): a value can be one of the types.
    - Intersection (`&`): a value must satisfy all types at once.

```ts
type Cat = { meow(): void };
type Dog = { bark(): void };
type CatOrDog = Cat | Dog; // either meow or bark
type CatAndDog = Cat & Dog; // both meow and bark
```

- **Best practices:** use discriminated unions for clarity; narrow types before accessing type-specific properties; prefer explicit unions over `any` for multiple possible types.


