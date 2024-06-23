---
title: "This is Perhaps"
slug: this-is-perhaps
description: "<p><strong>Perhaps</strong> is a lightweight <code>Maybe</code> type implementation for handling optional values in TypeScript. I designed the <code>Maybe</code> type to manage situations where values may be present or absent more gracefully and robustly, based on Haskall's <code>Maybe</code> type monad.</p>\n"
date: "2023-12-24"
---

# This is Perhaps

**Perhaps** is a lightweight `Maybe` type implementation for handling optional values in TypeScript. I designed the `Maybe` type to manage situations where values may be present or absent more gracefully and robustly, based on Haskall's `Maybe` type monad.

## Installation

Install **Perhaps** via npm:

```sh
npm install perhaps-ts
```

## Usage

### Creating `Maybe` Instances

Creating `Maybe` instances is straightforward. You can use the static methods `Just` and `Nothing` or their corresponding helper functions.

```typescript
import { Maybe, Just, Nothing } from "perhaps-ts";

// Using static methods
const maybeValue = Maybe.Just(5);
const noValue = Maybe.Nothing<number>();

// Using helper functions
const anotherMaybeValue = Just(10);
const anotherNoValue = Nothing<string>();
```

### Checking for Values

To determine if a `Maybe` instance has a value (`Just`) or is empty (`Nothing`), use the `IsJust` and `IsNothing` methods.

```typescript
if (maybeValue.IsJust()) console.log("This is Just:", maybeValue.FromJust());
if (noValue.IsNothing()) console.log("This is Nothing");
```

### Retrieving Values

To safely retrieve the value from a `Just` instance, use the `FromJust` or `ToChecked` methods. Be cautious, as these methods will throw an error if called on a `Nothing` instance.

```typescript
try {
    const value = maybeValue.FromJust();
    console.log("Value:", value);
} catch (error) {
    console.error(error.message);
}

try {
    const value = noValue.ToChecked();
} catch (error) {
    console.error(error.message); // Maybe: Attempted to access value of Nothing
}
```

### Default Values

Provide a default value when dealing with `Maybe` instances to ensure you always have a fallback.

```typescript
const valueOrDefault = noValue.FromMaybe(42);
console.log("Value or Default:", valueOrDefault); // Outputs: 42
```

### Conditional Value Extraction

Extract the value into an output object conditionally if the instance is `Just`.

```typescript
const output = {};
if (maybeValue.To(output)) {
    console.log("Extracted Value:", output.value); // Extracted Value: 5
} else {
    console.log("No value to extract");
}
```

Alternatively, use the `To` method with a custom key.

```typescript
type Output = { data?: number };

const output: Output = {};
if (maybeValue.To(output, "data")) {
    console.log("Extracted Data:", output.data); // Extracted Data: 5
} else {
    console.log("No data to extract");
}
```

### Equality Comparisons

Compare `Maybe` instances for equality with the `equals` and `notEquals` methods.

```typescript
const maybe1 = Just(5);
const maybe2 = Just(5);
const maybe3 = Nothing<number>();

console.log(maybe1.equals(maybe2)); // true
console.log(maybe1.equals(maybe3)); // false
console.log(maybe1.notEquals(maybe3)); // true
```

### Practical Function Examples

Here are some examples of how to write functions that return `Maybe<T>` instead of `T | false`, and how to handle these returned values.

#### Example Function Returning `Maybe<T>`

A function that searches for an item in an array and returns a `Maybe<T>` instead of the item or `false`.

```typescript
import { Maybe, Just, Nothing } from "perhaps-ts";

function findItem<T>(array: T[], predicate: (item: T) => boolean): Maybe<T> {
    for (const item of array) {
        if (predicate(item)) {
            return Just(item);
        }
    }
    return Nothing<T>();
}

// Usage example
const numbers = [1, 2, 3, 4, 5];

const result = findItem(numbers, (n) => n === 3);
if (result.IsJust()) {
    console.log("Found:", result.FromJust()); // Found: 3
} else {
    console.log("Item not found");
}
```

#### Handling and Checking `Maybe<T>` Values

Use `IsJust` and `IsNothing` to handle `Maybe<T>` values.

```typescript
const maybeNumber = findItem(numbers, (n) => n === 6);

if (maybeNumber.IsJust()) {
    console.log("Found:", maybeNumber.FromJust());
} else {
    console.log("Item not found");
}
```

Use `FromMaybe` with a default value.

```typescript
const valueOrDefault = maybeNumber.FromMaybe(0);
console.log("Value or Default:", valueOrDefault); // Value or Default: 0
```

Use `To` for conditional extraction.

```typescript
const output = {};
if (maybeNumber.To(output)) {
    console.log("Extracted Value:", output.value);
} else {
    console.log("No value to extract");
}
```

#### Another Function Example

A function that parses an integer from a string and returns `Maybe<number>`.

```typescript
function parseIntMaybe(input: string): Maybe<number> {
    const parsed = parseInt(input, 10);
    if (isNaN(parsed)) {
        return Nothing<number>();
    }
    return Just(parsed);
}

// Usage example
const maybeParsed = parseIntMaybe("123");
if (maybeParsed.IsJust()) {
    console.log("Parsed integer:", maybeParsed.FromJust()); // Parsed integer: 123
} else {
    console.log("Failed to parse integer");
}

const maybeFailedParsed = parseIntMaybe("abc");
console.log("Parsed or Default:", maybeFailedParsed.FromMaybe(0)); // Parsed or Default: 0
```

### Returning `Maybe<T>` from Asynchronous Functions

Using `Maybe<T>` in an asynchronous function.

```typescript
async function fetchData(url: string): Promise<Maybe<string>> {
    try {
        const response = await fetch(url);
        if (!response.ok) {
            return Nothing<string>();
        }
        const data = await response.text();
        return Just(data);
    } catch (error) {
        return Nothing<string>();
    }
}

// Usage example
fetchData("https://api.example.com/data").then((maybeData) => {
    if (maybeData.IsJust()) {
        console.log("Fetched Data:", maybeData.FromJust());
    } else {
        console.log("Failed to fetch data");
    }
});
```

## API Reference

### `Maybe` Class

#### Static Methods

-   `Maybe.Just(value: T): Maybe<T>`: Creates a `Just` instance containing the provided value.
-   `Maybe.Nothing<T>(): Maybe<T>`: Creates a `Nothing` instance with no value.

#### Instance Methods

-   `IsJust(): boolean`: Returns `true` if the instance is `Just`, otherwise `false`.
-   `IsNothing(): boolean`: Returns `true` if the instance is `Nothing`, otherwise `false`.
-   `FromJust(): T`: Returns the value if the instance is `Just`, otherwise throws an error.
-   `ToChecked(): T`: Alias for `FromJust`.
-   `FromMaybe(defaultValue: T): T`: Returns the value if the instance is `Just`, otherwise returns the provided default value.
-   `To(out: { value?: T }): boolean`: If the instance is `Just`, sets the value in the provided output object and returns `true`, otherwise returns `false`.
-   `equals(other: Maybe<T>): boolean`: Returns `true` if the other `Maybe` instance is equal, otherwise `false`.
-   `notEquals(other: Maybe<T>): boolean`: Returns `true` if the other `Maybe` instance is not equal, otherwise `false`.

---

I built **Perhaps** to simplify handling optional values in TypeScript, providing a clear and expressive way to manage cases where a value may or may not be present. It is built to help you write more robust and expressive code.

Cheers.
