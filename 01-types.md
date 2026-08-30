[← TypeScript Learning Notes](../README.md)

# 01 Types

## A. Core course notes

### What TypeScript adds to JavaScript

TypeScript is a superset of JavaScript that adds a static type system. It detects many type-related errors during development, before the code runs.

JavaScript is dynamically typed and does not perform this compile-time type-checking. Type-related problems may therefore appear at runtime or produce unexpected results through type coercion.

TypeScript makes intended types explicit and flags code that does not conform to them. In a development team, this communicates expectations and exposes where a type change affects other parts of the codebase.

Therefore TypeScript improves type safety, reliability and maintainability. It does not automatically make software secure or catch every kind of bug.

TypeScript checks types at compile time. When TypeScript is compiled into JavaScript, type annotations and other type-only information are removed. Therefore, TypeScript types are not enforced at runtime.

### Type inference

Type inference means TypeScript can determine a type without an explicit annotation. It uses information such as the value initially assigned to a variable and the context in which an expression appears

```ts
let firstName = 'Amira';
```

TypeScript infers that `firstName` is a string. Assigning a number later produces a type error

```ts
firstName = 27;
```

Inference can also use:

- A best common type when several possible types appear together
- Contextual typing based on where an expression is used, such as a function argument or return value

Type inference reduces unnecessary annotations while still giving the compiler type information

### Primitive data types

TypeScript recognises JavaScript primitive types covered in this module, including:

- `boolean`
- `number`
- `null`
- `string`
- `undefined`

If code assigns a value that is incompatible with the expected type, TypeScript reports an error

### Type annotations

A type annotation explicitly states the type expected for a variable

```ts
let mustBeAString: string;
mustBeAString = 'Catdog';
```

This assignment produces an error:

```ts
mustBeAString = 1337;
```

The syntax combines:

```ts
let variableName: type = value;
```

A type annotation is not the same as a type declaration. It adds type information to an existing declaration such as a variable or parameter

Annotations are particularly useful when:

- A variable is declared before its value is assigned
- The intended type cannot be inferred accurately
- An explicit contract improves clarity

### Type shapes

TypeScript uses a value's type to understand which properties and methods are available

```ts
const firstName = 'amira!';

console.log(firstName.toUpperCase());
console.log(firstName.length);
```

TypeScript can report a misspelled or unavailable member (property or method) before the code runs. 

- `.length` is a property
- `.toUpperCase()` is a method
- Both are members of the string type

This makes mistakes such as incorrect capitalisation or property names easier to locate.

For learning notes, I think the clearest wording is:


### The `any` type

`any` disables most type-checking for a value

```ts
let guess;

guess = 'blue';
guess = 27;
```

A value typed as `any` can be reassigned and used without the normal compiler checks. This is flexible but removes much of TypeScript's protection

`unknown` is usually safer when a value could be anything but must be examined before use. `any` is most appropriate as a deliberate and contained escape from type-checking

### TypeScript compiler

The TypeScript compiler checks TypeScript code and can produce JavaScript for execution. It removes TypeScript-only type information during this process

```powershell
tsc index.ts
```

The compiler can also check a project without producing JavaScript:

```powershell
tsc --noEmit
```

Successful type-checking means the compiler found no type errors under the active configuration. Logical and runtime errors can still remain

### Checking and running TypeScript

In the Codecademy exercise, I used:

```powershell
tsc
node index.js
```
tsc checked the types and compiled the TypeScript into JavaScript. If compilation succeeded, node index.js ran the generated JavaScript

For my local projects, I can separate type-checking from execution:

npx tsc --noEmit
npx tsx index.ts

npx tsc --noEmit checks the project without producing JavaScript files. npx tsx index.ts then runs the TypeScript file

Running a file successfully does not necessarily prove that it passed TypeScript type-checking. The type check needs to remain an explicit part of the development workflow


### `tsconfig.json`

A `tsconfig.json` file identifies a TypeScript project and configures how the compiler checks and emits its files

It can define:

- The JavaScript version targeted by emitted code
- The module system
- Strictness and null-checking rules
- Which files are included or excluded
- Whether and where JavaScript output is emitted

The file normally belongs at the project root. Running `tsc` without filenames makes the compiler locate and use the project configuration

Supplying source filenames directly to `tsc` causes the compiler to use command-line inputs rather than the project's `tsconfig.json`. A project-wide type check is therefore normally run through the configured project rather than by listing individual files

## B. Questions and deeper understanding

### Does TypeScript prevent developers from changing types?

No. Developers can intentionally change type definitions as requirements change. TypeScript's value is that it exposes the consequences by reporting incompatible uses elsewhere in the codebase

Developers can also bypass checking through:

- `any`
- Type assertions
- Non-null assertions
- Suppression comments such as `@ts-ignore`
- Weaker compiler settings
- Tools that transform or execute TypeScript without running a type check

These are escape hatches rather than evidence that the type system has failed. Responsible use is narrow, explained and justified

Expanded notes about guards, assertions and runtime behaviour are in [Type Narrowing](05-type-narrowing.md)

### When can `any` be appropriate?

Reasonable uses include:

1. __Gradually migrating JavaScript__

   Temporary `any` annotations can allow a large codebase to move to TypeScript in manageable stages

2. __Integrating an untyped JavaScript library__

   A small adapter can contain `any` internally while exposing typed functions to the rest of the application

3. __Working around incorrect third-party types__

   A narrowly scoped and documented `any` can bridge a temporary mismatch between runtime behaviour and published definitions

4. __Testing runtime validation__

   A test may deliberately pass malformed data to confirm that runtime validation rejects it

#### More advanced examples to revisit

To revisit after completing the TypeScript foundations:

5. __Describing any callable signature in advanced reusable utilities__

   Some library-level function constraints need to accept many different parameter and return types.  A callable signature describes how a function can be called, including its parameters and return value. Some reusable library utilities need to work with many different kinds of functions

   __Revisit after learning__: function types, generics and utility types

6. __Highly dynamic code__

   Reflection, proxies or metaprogramming may occasionally interact with structures that cannot be modelled accurately in advance. Some code discovers or changes object behaviour at runtime. Reflection, proxies and metaprogramming are examples of this kind of dynamic programming

   __Revisit after learning__: objects, generics and advanced TypeScript patterns

`any` is not a good shortcut for an error that is difficult to understand. For genuine uncertainty, prefer `unknown` and narrow or validate the value before using it

### Is TypeScript more secure than JavaScript?

TypeScript can reduce defects by identifying incompatible types and making contracts explicit, but type safety is not the same as information security

The compiler does not validate real API responses, user input or database values at runtime. Security and data integrity still require runtime validation, authentication, authorisation, safe database access and other controls appropriate to the system

### Is a TypeScript non-null assertion like SQL `NOT NULL`?

The comparison revealed an important difference:

- A TypeScript non-null assertion tells the compiler to trust the developer
- A SQL `NOT NULL` constraint actively rejects invalid database writes at runtime

The expanded comparison is in [Type Narrowing: Non-null assertions and database constraints](05-type-narrowing.md#non-null-assertions-and-database-constraints)

### What validates the types of information returned by an API?

This question led from compile-time types into runtime validation, type guards, schema validation and contract testing

See [Type Narrowing: Runtime validation of API data](05-type-narrowing.md#runtime-validation-of-api-data) and [Contract testing](05-type-narrowing.md#how-contract-testing-relates)

### Are broad truthiness checks an example of typed languages like Java being stricter than TypeScript?

Yes, Java requires conditions to produce a Boolean, while TypeScript retains JavaScript's truthiness rules. The fuller comparison belongs with narrowing and Boolean guards

See [Type Narrowing: Truthiness checks and Java](05-type-narrowing.md#truthiness-checks-and-java)

## C. Verification and further learning

### Core references

- [TypeScript documentation](https://www.typescriptlang.org/docs/)
- [Type inference](https://www.typescriptlang.org/docs/handbook/type-inference.html)
- [`tsconfig.json`](https://www.typescriptlang.org/tsconfig/)
- [Compiler options](https://www.typescriptlang.org/docs/handbook/compiler-options.html)

### Type-checking and execution tools

Transformation and type-checking are separate responsibilities

- `tsc` performs TypeScript type-checking and can emit JavaScript
- `tsx` runs TypeScript through fast transformation but does not replace a separate type check
- Vite transpiles TypeScript but expects type-checking to happen elsewhere
- esbuild and Babel remove TypeScript syntax without performing full TypeScript type-checking
- Node's built-in type stripping can run supported TypeScript syntax without checking types
- `ts-node` type-checks by default, but `--transpileOnly` and `--swc` skip it

Useful documentation:

- [`ts-node` options](https://typestrong.org/ts-node/docs/options/)
- [Vite TypeScript support](https://vite.dev/guide/features#typescript)
- [esbuild TypeScript support](https://esbuild.github.io/content-types/#typescript)
- [Babel TypeScript transform](https://babeljs.io/docs/babel-plugin-transform-typescript)
- [Node.js TypeScript support](https://nodejs.org/api/typescript.html)

The planned local workflow separates the two jobs:

```powershell
npx tsc --noEmit
npx tsx index.ts
```

The first command checks types. The second executes the program

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example [https://www.lcampbell.dev/ August 2026]

My Astro portfolio uses both inferred and explicitly declared types. TypeScript can infer simple types from assigned values, so declarations such as const SCROLL_THRESHOLD = 20 are understood as numbers without an annotation.

I used explicit types when the expected value was less obvious, particularly when working with browser APIs:

const toggle = document.getElementById(
  "menu-toggle",
) as HTMLButtonElement;

function trapFocus(event: KeyboardEvent) {
  // ...
}

function getTheme(): string {
  return document.documentElement.getAttribute("data-theme") ?? "light";
}

These examples demonstrate:

Type inference for strings, numbers and arrays
DOM element types such as HTMLButtonElement
Event types such as KeyboardEvent
Explicit function return types

TypeScript inference works particularly well for local values whose types are obvious from their initial values. For example, it can infer that `SCROLL_THRESHOLD` is a number and that `siteUrl` is a string.

Explicit types become more valuable at boundaries, such as component properties, function parameters, browser APIs and external data. My portfolio therefore uses inference for simple internal values and explicit types where code interacts with Astro or the DOM.

Portfolio reference: src/components/navigation/Header.astro


### Restaurant Recommender

__Purpose__: repair deliberately incorrect types and complete filtering logic for a restaurant recommendation program

#### What I applied

- Defined the required structure of restaurant objects
- Typed an array so every restaurant had to match that structure
- Added and corrected variable type annotations
- Used compiler errors to locate incompatible values and incorrect property names
- Added filtering for price, delivery time, distance and opening hours

#### Errors and debugging

- Object property type definitions had been written without belonging to a named type
- Types had been placed where object values were required
- A `const` variable was declared without its initial value and assigned later
- A result variable was declared more than once while adding its type
- Parentheses closed before the complete Boolean expression in an `if` condition
- Numeric comparisons used values represented as different types
- A completed Boolean comparison was unnecessarily converted to a number
- A property name did not match the restaurant objects

#### What I learned

- A type definition describes which values are allowed, while an object contains the actual values
- A `const` must receive its value when declared
- A variable needs one declaration, with later branches assigning its value
- The entire Boolean expression belongs inside the `if` parentheses
- Successful compilation does not prove that the remaining logic is correct
- Compiler messages become easier to use when I separate syntax, type and logic problems

The interface and object-typing questions belong in [Advanced Object Types](06-advanced-object-types.md)


### Local execution setup

The code passed `tsc` in Codecademy, but the local repository exposed a compatibility issue between the installed TypeScript and `ts-node` versions. This was a tooling problem rather than an error in the exercise code

For local projects, I plan to check the types with:

```powershell
npx tsc --noEmit
```

This runs the locally installed TypeScript compiler and checks the project without generating JavaScript files

If the type check succeeds, I can run the TypeScript file with:

```powershell
npx tsx index.ts
```

`tsx` executes the TypeScript file but does not perform full TypeScript type-checking. Keeping these as separate commands ensures that successfully running the program is not mistaken for successfully passing the type check

### TypeMart

__Status__: Prepared as the next Types mini-project. The supplied product data and course task remain in the private practice repository

## E. Seed Keeper connections and later exploration

### Apply from the Types module

- Use explicit domain types to communicate intended values
- Use inference where the compiler already has enough information
- Avoid allowing `any` to spread through the application
- Keep TypeScript types aligned with database nullability and constraints
- Run a separate type check even when the chosen development tool can execute TypeScript directly

### Explore later

- Configure ESLint for TypeScript
- Explore lint rules for strict Boolean expressions
- Explore rules that restrict `any`, non-null assertions and suppression comments
- Explore runtime schema-validation libraries after completing the fundamentals
- Explore how TypeScript types, runtime validation, API schemas and contract testing work together

## F. Some AI Suggested Retrieval prompts

- What is the difference between static type-checking and runtime validation?
- What information can TypeScript infer from an initial value?
- When is a type annotation useful?
- Why does `any` weaken type safety?
- When can `any` be a justified, contained escape hatch?
- What happens to TypeScript types when JavaScript is emitted?
- What is the difference between type-checking and executing TypeScript?
- Why does changing a type definition expose consequences rather than prevent change?
- Why can SQL enforce `NOT NULL` while a TypeScript assertion cannot?
