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

## D. Applied learning: mini-projects and debugging

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
