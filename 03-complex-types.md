[← TypeScript Learning Notes](../README.md)

# 03 Complex Types

__Course status__: Not started

## A. Core course notes

### Preview from Restaurant Recommender: arrays of structured objects

The Restaurant Recommender exercise introduced an array whose items all followed the same object structure

```ts
const restaurants: Restaurant[] = [
  // restaurant objects
];
```

`Restaurant[]` means every item in the array must satisfy the `Restaurant` type. TypeScript can therefore report missing properties, incorrect property names and incompatible values in any item.

This idea will be expanded after completing the Complex Types module. The detailed comparison between interfaces and type aliases belongs in [Advanced Object Types](06-advanced-object-types.md)

## B. Questions and deeper understanding

### What does `Restaurant[]` communicate?

It describes the type of the whole collection, not one runtime value. It tells developers and the compiler that the collection contains restaurant objects with a consistent structure.

This question first arose while debugging the [Restaurant Recommender](01-types.md#restaurant-recommender) during the Types module

## C. Verification and further learning

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example [https://www.lcampbell.dev/ August 2026]

The portfolio stores structured content in arrays of objects. TypeScript infers the shape of each object and the type of nested arrays.

const courses = [
  {
    provider: "Code First Girls",
    title: "CFGdegree Data and Software Engineering",
    year: "2026",
    summary: "Developed software with Python, SQL and REST APIs...",
    topics: [
      "APIs and microservices",
      "Object-oriented programming",
      "Data structures and libraries",
    ],
  },
];

From this value, TypeScript can infer that:

courses is an array
Each course is an object
provider, title, year and summary are strings
topics is an array of strings
The course parameter inside .map() has the inferred course structure

The experience, navLinks and principles arrays use the same pattern.

TypeScript can infer the structure of the `courses` array from its initial objects. This is convenient for local static data, but the inferred structure describes what the objects currently contain rather than documenting what they are intended to contain.

An explicit `Course` interface would be more useful if the data moved into another file, came from an API or allowed optional fields. For example, `year?: string` would document that a course may omit its year and would explain the conditional rendering in the template.

Portfolio references:

src/components/sections/TechEducation.astro
src/components/sections/Experience.astro
src/components/navigation/Header.astro
src/components/sections/About.astro


### Restaurant Recommender

The exercise provided an early practical example of typing an array of objects before this module was reached. The Rstarter code contained an array of restaurant objects. While debugging it, I introduced a `Restaurant` interface and annotated the array:

```ts
const restaurants: Restaurant[] = [
  // restaurant objects
];

See [Types: Restaurant Recommender](01-types.md#restaurant-recommender)

## E. Seed Keeper connections and later exploration

## F. Retrieval prompts

- What does `Type[]` describe?
- What can TypeScript check when every array item shares a declared structure?
