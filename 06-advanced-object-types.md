[← TypeScript Learning Notes](../README.md)

# 06 Advanced Object Types

__Course status__: Not started —  questions recorded

## A. Core course notes

### Preview: defining an object structure

The Restaurant Recommender exercise required a named structure for restaurant objects

```ts
interface Restaurant {
  name: string;
  priceBracket: number;
}
```

The interface describes which properties must exist and which value types they accept. Runtime objects then supply the actual values

```ts
{
  name: 'Example Restaurant',
  priceBracket: 2,
}
```

These have different roles:

```ts
priceBracket: number; // property type in the interface
priceBracket: 2,      // property value in an object
```

### Interfaces and type aliases

Both an interface and a type alias can describe required object properties

An interface is a type declaration with its own syntax:

```ts
interface Restaurant {
  name: string;
}
```

This declares that `Restaurant` has the specified structure

A type alias uses `=` to connect a name to the type it represents:

```ts
type Restaurant = {
  name: string;
};
```

This makes `Restaurant` another name for that type. The equals sign is type syntax rather than a runtime assignment

The broader distinction discussed so far is:

- Interfaces mainly describe object and class structures
- Type aliases can also name unions, primitives, tuples and other type expressions

For example:

```ts
type RestaurantStatus = 'open' | 'closed';
```

The fuller similarities and differences will be expanded after completing this module

### Arrays of typed objects

```ts
const restaurants: Restaurant[] = [
  // restaurant objects
];
```

This tells TypeScript to check every array item against the `Restaurant` structure

The collection aspect is also recorded in [Complex Types](03-complex-types.md#preview-from-restaurant-recommender-arrays-of-structured-objects)

### Interfaces and object-oriented programming (OOP)

Using an interface does not automatically make code object-oriented

The Restaurant Recommender used ordinary JavaScript object literals. It did not use classes, constructors, encapsulation, inheritance or polymorphism

A precise description is:

> Using a TypeScript interface to define and check the structure of JavaScript objects

Interfaces support object-oriented designs because classes can implement them. They are also useful in non-OOP code. Like other TypeScript type information, interfaces disappear when the code is compiled to JavaScript

## B. Questions and deeper understanding

### Why does an interface not use an equals sign?

An interface has its own declaration syntax. The name and structure form one declaration

A type creates an alias, so its syntax uses `=` to associate the alias with the represented type

This is a syntax distinction, not a statement that one approach assigns a runtime value

### Does using an interface make the program OOP?

No. An interface is part of TypeScript's type system. Whether a program is object-oriented depends on the wider design and use of concepts such as objects with behaviour, classes, encapsulation, inheritance or polymorphism

This question arose during the [Restaurant Recommender](01-types.md#restaurant-recommender) before the Advanced Object Types module was reached

## C. Verification and further learning

- [TypeScript documentation: Object types](https://www.typescriptlang.org/docs/handbook/2/objects.html)

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example [https://www.lcampbell.dev/ August 2026]

The portfolio uses an interface to describe the object accepted by BaseLayout.astro:

interface Props {
  title: string;
  description?: string;
  canonicalUrl?: string;
}

const {
  title,
  description = "L Campbell — Software engineer...",
  canonicalUrl,
} = Astro.props;

The interface establishes a reusable contract:

title is required
description is optional
canonicalUrl is optional
Consumers receive editor suggestions and type checking
Destructuring can provide defaults for missing optional values

The inferred course structure could also be made explicit:

interface Course {
  provider: string;
  title: string;
  year?: string;
  summary: string;
  topics: string[];
}

const courses: Course[] = [
  // Course objects
];

The header also supplies object types to generic DOM methods:

const navLinks =
  menu.querySelectorAll<HTMLAnchorElement>(
    ".mobile-menu__nav-link",
  );

This tells TypeScript that each item in the returned collection is an HTMLAnchorElement, giving access to link-specific properties with type checking.

TypeScript uses structural typing. A value satisfies the `Props` interface when it has the required property structure; it does not need to be created from a `Props` class.

This suits Astro component properties because the component cares about the shape of the supplied data rather than how the object was created.

#### Generic DOM element types

If generics are not taught in this course module, then do **Further learning: generic DOM types** 

The header uses a generic DOM method to specify the type of elements returned by a selector:

```ts
const navLinks =
  menu.querySelectorAll<HTMLAnchorElement>(
    ".mobile-menu__nav-link",
  );

HTMLAnchorElement is passed to querySelectorAll() as a generic type argument. TypeScript therefore gives the result the type:

NodeListOf<HTMLAnchorElement>

This means TypeScript knows that each item in navLinks is an anchor element and provides access to link-specific properties and methods.

This demonstrates how existing object types can be supplied to generic functions to make their returned values more precise.



Portfolio references:

src/layouts/BaseLayout.astro
src/components/sections/TechEducation.astro
src/components/navigation/Header.astro


### Restaurant Recommender

The project introduced interfaces and typed object arrays before this module was reached

See [Types: Restaurant Recommender](01-types.md#restaurant-recommender)

## E. Seed Keeper connections and later exploration

## F. Retrieval prompts

- What is the difference between a property type and a property value?
- Why does a type alias use `=` while an interface does not?
- What can a type alias represent beyond an object structure?
- What does `Restaurant[]` require from every array item?
- Why does an interface not automatically make a program object-oriented?
