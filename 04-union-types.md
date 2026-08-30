[← TypeScript Learning Notes](../README.md)

# 04 Union Types

__Course status__: Not started —  questions recorded

## A. Core course notes

### Preview: combining possible types

A union represents a value that can belong to more than one permitted type

```ts
string | number
```

The value must satisfy at least one member of the union. Code can only use operations that are safe for the value's currently known type until it has been narrowed

### Literal unions

A type alias can combine specific literal values:

```ts
type RestaurantStatus = 'open' | 'closed';
```

This is more precise than `string` because only the named values are permitted

### Preview: discriminated unions

Related object types can share a property whose literal value identifies which object is present

```ts
type Result =
  | { status: 'success'; data: string }
  | { status: 'error'; message: string };
```

The `status` property acts as the discriminant. Checking it allows TypeScript to identify the corresponding object type

The narrowing behaviour is explained in [Type Narrowing: Discriminated unions](05-type-narrowing.md#discriminated-unions)

## B. Questions and deeper understanding

### How are unions connected to type guards?

A union records the possible types. A type guard gathers runtime evidence that identifies which member is present at a particular point in the program

This connection arose during the Types module before the Union Types and Type Narrowing modules had been reached

## C. Verification and further learning

- [TypeScript documentation: Union types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types)

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example [https://www.lcampbell.dev/ August 2026]

Optional properties implicitly create union types. In BaseLayout.astro, the component’s Props interface includes optional properties:

interface Props {
  title: string;
  description?: string;
  canonicalUrl?: string;
}

These types can be understood as:

description: string | undefined;
canonicalUrl: string | undefined;

The nullish coalescing operator provides a string when the optional value is absent:

const canonical =
  canonicalUrl ?? `${siteUrl}${Astro.url.pathname}`;

The theme feature could also use a literal union:

type Theme = "light" | "dark";

This is more precise than string because it prevents unrelated values such as "blue" from being used as themes.

?? compared with ||
The nullish coalescing operator uses the fallback only when `canonicalUrl` is `null` or `undefined`. The logical OR operator would also replace other falsy values such as an empty string, `0` or `false`.

This matters when working with union types because it allows the code to handle absence without automatically rejecting every falsy value.

Portfolio references:

src/layouts/BaseLayout.astro
src/components/navigation/Header.astro


## E. Seed Keeper connections and later exploration

## F. Retrieval prompts

- What does a union type permit?
- How is a literal union more precise than `string`?
- What property makes an object union discriminated?
- Why does a union often need narrowing before type-specific operations are safe?
