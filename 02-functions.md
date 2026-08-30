[← TypeScript Learning Notes](../README.md)

# 02 Functions

__Course status__: Not started

## A. Core course notes

## B. Questions and deeper understanding

## C. Verification and further learning

## D. Applied learning: portfolio website, mini-projects and debugging

### Portfolio example (https://github.com/LCampbellDev/Portfolio [August 2026])

The portfolio uses function declarations, arrow functions and callback functions. TypeScript can check function parameters and return values while often inferring types from context.

function trapFocus(event: KeyboardEvent) {
  if (event.key !== "Tab") return;

  // ...
}

The event parameter is explicitly typed as KeyboardEvent. Functions such as openMenu() and closeMenu() do not declare return types, but TypeScript infers void because they perform actions without returning values.

The portfolio also uses arrow functions:

const getFocusable = () =>
  Array.from(
    menu.querySelectorAll<HTMLElement>(
      'a[href], button, [tabindex]:not([tabindex="-1"])',
    ),
  ).filter((element) => !element.hasAttribute("disabled"));

Callback functions appear throughout the components:

courses.map((course) => /* template */);

navLinks.forEach((link) =>
  link.addEventListener("click", closeMenu),
);

Passing `closeMenu` gives the event listener a reference to the function so the browser can call it later. Writing `closeMenu()` would execute the function immediately and pass its return value instead.

This distinction is important when using callbacks, event listeners and array methods. TypeScript can check whether the supplied function has a compatible parameter and return structure.

Portfolio references:

src/components/navigation/Header.astro
src/components/sections/TechEducation.astro
src/components/sections/Experience.astro


## E. Seed Keeper connections and later exploration

## F. Retrieval prompts
