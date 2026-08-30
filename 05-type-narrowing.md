[← TypeScript Learning Notes](../README.md)

# 05 Type Narrowing

__Course status__: Not started —  questions recorded

## A. Core course notes

### Preview: type narrowing

Type narrowing is the process of reducing a broad set of possible types to a more specific type using evidence from the program's control flow

```ts
function printValue(value: string | number) {
  if (typeof value === 'string') {
    console.log(value.toUpperCase());
  } else {
    console.log(value.toFixed(2));
  }
}
```

Inside the first branch, TypeScript knows that `value` is a string. In the second branch, it knows the remaining possibility is a number

### Type guards

A type guard is a runtime check that gives TypeScript enough evidence to narrow a value

#### `typeof`

`typeof` is useful for primitive values:

```ts
if (typeof value === 'string') {
  // value is a string here
}
```

JavaScript has an important peculiarity:

```ts
typeof null === 'object';
```

An object check therefore often needs to exclude `null` explicitly

#### `instanceof`

`instanceof` checks whether an object is connected to a runtime class or constructor:

```ts
if (error instanceof Error) {
  console.log(error.message);
}
```

It can work with classes, `Error`, `Date` and DOM element constructors. It cannot check an interface because interfaces do not exist at runtime

#### The `in` operator

The `in` operator checks whether a property exists on an object:

```ts
if ('name' in value) {
  // value has a name property
}
```

Existence does not prove that the property's value has the expected type. External data usually requires additional checks

#### `Array.isArray()`

```ts
if (Array.isArray(value)) {
  console.log(value.length);
}
```

This establishes that the value is an array, but not the types of its items

#### Equality checks

Checking a value against a literal can narrow a union:

```ts
type Status = 'loading' | 'success' | 'error';

if (status === 'success') {
  // status is exactly 'success' here
}
```

### Discriminated unions

A discriminated union uses a shared property with different literal values to distinguish related object types

```ts
type Result =
  | { status: 'success'; data: string }
  | { status: 'error'; message: string };
```

```ts
function displayResult(result: Result) {
  if (result.status === 'success') {
    console.log(result.data);
  } else {
    console.log(result.message);
  }
}
```

Checking `status` narrows the complete object, not only the one property

See [Union Types](04-union-types.md#preview-discriminated-unions) for how the possible object types are modelled

### Null and undefined guards

A value such as `User | undefined` can be narrowed through an explicit check:

```ts
if (user !== undefined) {
  console.log(user.name);
}
```

A truthiness check also removes falsy possibilities, but may unintentionally exclude valid values such as `0`, an empty string or `false`

### Custom type guards

A reusable guard can perform a runtime check and describe what a successful result means to TypeScript

```ts
function isString(value: unknown): value is string {
  return typeof value === 'string';
}
```

`value is string` is a type predicate. At runtime, the function returns a Boolean. At compile time, TypeScript understands that a `true` result means the value can be treated as a string

Custom guards are useful for:

- API responses
- Parsed JSON
- Union types
- Reusable validation
- Filtering mixed arrays

TypeScript trusts a custom predicate, so its implementation must genuinely prove the type it claims

### Assertion functions

An assertion function throws when a value is invalid and tells TypeScript what is known after successful completion

```ts
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== 'string') {
    throw new Error('Expected a string');
  }
}
```

The distinction is:

- A type guard returns `true` or `false` so the caller decides what happens
- An assertion function stops execution for invalid data and continues only with a valid value

## B. Questions and deeper understanding

### When is a type assertion appropriate?

A type assertion is appropriate when the developer has reliable information that TypeScript cannot infer

```ts
value as SomeType
```

It does not validate or convert the runtime value. It transfers responsibility from the compiler to the developer

Reasonable uses include:

- A DOM element whose more specific type is guaranteed by controlled markup
- A broadly typed event whose source is guaranteed by the application
- Incomplete or inaccurate third-party type definitions
- Data that has already passed runtime validation which TypeScript cannot understand
- Carefully scoped test objects
- `as const` to preserve narrow literal types

Prefer, in order:

1. Type inference
2. An accurate annotation
3. A condition or type guard
4. Runtime validation for external data
5. `satisfies` when checking an object's compatibility without replacing its inferred type
6. A type assertion when the compiler genuinely lacks available evidence

Avoid assertions that:

- Silence an unexplained error
- Pretend unvalidated API data is safe
- Force an incomplete object to satisfy an interface
- Remove `null` or `undefined` without evidence
- Convert unrelated types through a double assertion

### Type guard versus type assertion

The difference is evidence:

- A type assertion tells the compiler what to assume without a runtime check
- A type guard checks the real value and narrows it only when the check succeeds

This question first arose while exploring how TypeScript checks can be bypassed in [Types](01-types.md#does-typescript-prevent-developers-from-changing-types)

### Runtime validation of API data

An API can return valid JSON that does not match the structure expected by the application. Neither a successful HTTP response nor JSON parsing proves that the data has the correct fields and values

Runtime validation can check:

- The top-level kind of value
- Required properties
- Primitive property types
- Allowed literal values
- Numeric ranges and string formats
- Array item types
- Nested object structures

A manual custom guard can validate the real value and narrow it. Larger applications often use schema-validation libraries to define and run these checks consistently

A safe boundary is:

1. Receive external data as `unknown`
2. Validate its structure and values at runtime
3. Reject or handle invalid data
4. Use the validated result as an internal TypeScript type

Schema-validation libraries are recorded as a later exploration topic rather than part of the current fundamentals work

### Non-null assertions and database constraints

A TypeScript non-null assertion:

```ts
user!.name
```

tells the compiler to trust that `user` is neither `null` nor `undefined`. It does not change or check the runtime value

A SQL `NOT NULL` constraint actively rejects a database insert or update that would store `NULL`

Persistent invariants are normally represented at several layers:

- TypeScript types communicate requirements and catch mistakes during development
- Application validation rejects bad input early and produces useful messages
- Database constraints protect the stored data regardless of where a write originates

The database remains the final authority for persistent data integrity. The application's types should normally agree with the database's nullability

This question arose while considering the non-null assertion during the [Types module](01-types.md#is-a-typescript-non-null-assertion-like-sql-not-null)

### How contract testing relates

Contract testing is related to runtime data structures but operates at a different level

| Mechanism | Question it answers |
| --- | --- |
| TypeScript types | Does our code use values consistently? |
| Runtime schema validation | Does this actual payload contain valid data? |
| Contract testing | Do the provider and consumer agree about the API? |
| Database constraints | Can invalid persistent data be stored? |

A contract test can check that an API provides agreed response statuses, property names, required fields, types and behaviours. It can expose an incompatible provider change during testing or CI

Contract testing does not normally inspect every production response. Runtime validation checks the actual payload received by the running application. The two approaches complement each other

### Truthiness checks and strongly typed languages like Java

JavaScript and TypeScript allow values to be converted to Booleans in conditions

Falsy values include:

- `false`
- `0`
- An empty string
- `null`
- `undefined`
- `NaN`

A broad truthiness check can therefore remove values that are present and valid but happen to be falsy

Java generally requires an `if` condition to produce an actual Boolean. A string, number or object cannot silently become true or false. This forces developers to distinguish absence, zero, an empty string and `false` more explicitly

TypeScript adds static checks but preserves JavaScript's runtime truthiness behaviour. Java is stricter in this specific area, although Java references can still be `null` and cause runtime failures

### Compiler rules versus lint rules

TypeScript checks whether code satisfies the language's configured type rules. A linter can enforce additional team policies for code that TypeScript technically permits

TypeScript-aware ESLint rules can be configured to:

- Require stricter Boolean expressions
- Restrict explicit `any`
- Restrict non-null assertions
- Restrict TypeScript suppression comments
- Require promises to be handled
- Detect unsafe use of `any`

This makes linting a useful later layer for SeedKeeper rather than another branch to study before completing the fundamentals

## C. Verification and further learning

- [TypeScript documentation: Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)

### Parked research

- Compare runtime schema-validation libraries after completing the TypeScript fundamentals
- Explore contract-testing approaches when SeedKeeper has a provider–consumer boundary
- Explore TypeScript-aware ESLint rules when configuring the first personal TypeScript project

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example (https://github.com/LCampbellDev/Portfolio [August 2026])

#### Narrowing browser API values

My Astro portfolio uses browser APIs that can return more than one possible type. Type narrowing allows code to check a value at runtime before treating it as a more specific type.

#### Theme preference validation + The anti-flash script in `<head>` in BaseLayout.astro 

`BaseLayout.astro` contains an anti-flash script in the document `<head>`. It reads the saved theme and applies it before the page renders, preventing users from briefly seeing the wrong colour scheme (and flashing).

The script uses `is:inline` because it needs to execute immediately rather than wait for a separate bundled file. Astro therefore sends it to the browser exactly as written and does not process its contents as TypeScript.

Although this particular script is JavaScript, its validation logic provides a useful model for TypeScript narrowing. It checks data retrieved from browser localStorage before allowing that value to control the page theme:

const saved = localStorage.getItem("theme");

if (saved === "light" || saved === "dark") {
  theme = saved;
}

localStorage.getItem() can return:

string | null

It returns a string when the key exists and null when it does not. The stored string could also contain an unexpected value.

The condition checks that saved is specifically "light" or "dark". Inside the conditional block, the value has been narrowed from a general string | null to one of the two accepted values.

The script uses is:inline, so Astro sends it to the browser as raw JavaScript rather than processing it as TypeScript. However, the runtime validation demonstrates the same narrowing logic that I could express explicitly in TypeScript:

type Theme = "light" | "dark";

const saved = localStorage.getItem("theme");

if (saved === "light" || saved === "dark") {
  const theme: Theme = saved;
}

The check protects the application from:

A missing local storage value
An unexpected stored string
Assigning an invalid value to the theme
Assuming that browser storage always contains valid application data

A TypeScript version would provide stronger development-time checks, but the anti-flash requirement makes a small inline JavaScript script the more appropriate implementation. Runtime validation remains necessary in either language.

Portfolio reference: src/layouts/BaseLayout.astro

#### Narrowing a theme value

The processed TypeScript script in Header.astro currently returns the theme as a general string:

function getTheme(): string {
  return (
    document.documentElement.getAttribute("data-theme") ??
    "light"
  );
}

getAttribute() returns string | null. The nullish coalescing operator supplies "light" when the attribute is null, so the final result is always a string.

A stricter future version could combine a literal union with narrowing:

type Theme = "light" | "dark";

function getTheme(): Theme {
  const theme =
    document.documentElement.getAttribute("data-theme");

  if (theme === "dark") {
    return theme;
  }

  return "light";
}

This version makes "light" and "dark" the only values that the function can return. It would also allow other theme functions to accept Theme instead of the broader string type:

function updateToggleLabel(theme: Theme) {
  const nextTheme: Theme =
    theme === "dark" ? "light" : "dark";

  // Update the visible and accessible labels
}

This would provide a small but meaningful improvement if the theme functionality became more complex.

Portfolio reference: src/components/navigation/Header.astro

#### DOM element assertions and narrowing

The header script retrieves several required DOM elements:

const toggle = document.getElementById(
  "menu-toggle",
) as HTMLButtonElement;

Without the assertion, getElementById() returns:

HTMLElement | null

This is because TypeScript cannot know whether an element with that ID exists in the rendered page.

The as HTMLButtonElement syntax is a type assertion. It tells TypeScript to treat the result as an HTMLButtonElement, but it does not check the value at runtime. A type assertion is therefore different from type narrowing.

A runtime narrowing version would be:

const toggle =
  document.getElementById("menu-toggle");

if (!(toggle instanceof HTMLButtonElement)) {
  throw new Error("Menu toggle button not found");
}

toggle.focus();

Before the instanceof check, toggle has the type:

HTMLElement | null

After the check, TypeScript knows that it is an:

HTMLButtonElement

The check handles both possibilities that would make the element unsuitable:

The value is null
The element exists but is not a button
Implementation decision

#### Implementation decision

I considered replacing the header’s DOM assertions with runtime narrowing. I decided not to change them solely to demonstrate a TypeScript feature.

The current assertions are proportionate because:

The required elements and their script are defined together in Header.astro
The elements are always rendered as part of the same static component
Their IDs are controlled within the project
The menu cannot function meaningfully if its required elements are absent
Runtime checks would add code but could not restore missing header functionality

The assertions therefore document an invariant: these elements must exist whenever the header script runs.

I would reconsider this decision if:

The header became reusable across substantially different layouts
Any of the elements became conditional
The script moved into a separate module
External code supplied or modified the markup
The application needed to recover gracefully when an element was absent

This demonstrates that type narrowing is not something to add mechanically. The decision depends on the possible runtime states, the level of risk and whether the application can respond meaningfully when a check fails.

Portfolio reference: src/components/navigation/Header.astro

#### Learning summary

This portfolio code helped me distinguish between three related approaches:

Nullish coalescing provides a fallback when a value is null or undefined
Type narrowing checks a runtime value before TypeScript treats it as a more specific type
Type assertions tell TypeScript what type to assume without performing a runtime check

The theme preference benefits from runtime validation because browser storage can contain missing or unexpected data. The required header elements use assertions because their presence is controlled by the same static component.

The appropriate choice depends on the source of the value, the realistic failure states and whether additional runtime handling would improve the behaviour of the application.


## E. Seed Keeper connections and later exploration

### Explore in the first personal TypeScript project

- Configure ESLint for TypeScript
- Evaluate strict Boolean-expression rules
- Restrict unsafe escape hatches where appropriate
- Validate external data at system boundaries
- Keep database constraints, application validation and TypeScript models aligned
- Consider contract testing only when the architecture contains a meaningful provider–consumer contract

## F. Retrieval prompts

- What evidence does `typeof` provide to TypeScript?
- Why can `instanceof` check a class but not an interface?
- What does the `in` operator prove, and what does it not prove?
- How does a discriminated union allow TypeScript to narrow a whole object?
- What does a custom type predicate communicate?
- How does an assertion function differ from a Boolean type guard?
- Why is a type assertion not runtime validation?
- What checks are needed before external data becomes a trusted internal type?
- How do runtime validation and contract testing differ?
- Why can a broad truthiness check accidentally remove valid values?
- What is the difference between a compiler rule and a lint rule?
