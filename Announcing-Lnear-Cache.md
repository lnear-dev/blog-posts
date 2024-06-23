---
title: "Introducing @lnear/cache: Efficient Caching for TypeScript"
slug: introducing-at-lnearcache-efficient-caching-for-typescript
description: "<p><code>@lnear/cache</code> is a TypeScript library designed to bring efficient caching strategies to your applications. Caching improves performance by storing frequently accessed data in memory, reducing the need to fetch or compute data repeatedly.</p>\n"
date: "2023-12-24"
---

# Introducing @lnear/cache: Efficient Caching for TypeScript

`@lnear/cache` is a TypeScript library designed to bring efficient caching strategies to your applications. Caching improves performance by storing frequently accessed data in memory, reducing the need to fetch or compute data repeatedly.

## Why Use Caching?

Rapid application development requires performance optimization and caching helps to achieve:

-   **Faster Response Times**: By storing frequently accessed data in memory, caching reduces the latency of fetching data from slower storage systems.
-   **Reduced Server Load**: Caching minimizes the computational load on servers by serving cached data instead of recalculating or querying it repeatedly.
-   **Improved Scalability**: Efficient caching strategies ensure applications can handle increased traffic and scale gracefully.

## Introducing @lnear/cache

`@lnear/cache` provides two powerful caching strategies out of the box:

### Least Recently Used (LRU) Cache

LRU caching ensures that the least recently accessed items are evicted when the cache reaches its maximum capacity. This strategy is ideal for scenarios where older data is less likely to be accessed again soon.

### Least Frequently Used (LFU) Cache

LFU caching evicts the least frequently accessed items when the cache size limit is reached. This strategy is beneficial for scenarios where the frequency of access determines the relevance of data.

## Getting Started

### Installation

To start using `@lnear/cache` in your TypeScript project, simply install it via npm:

```bash
npm install @lnear/cache
```

### Example Usage

#### LRU Cache Example

```typescript
import { LRUCache } from "@lnear/cache";

// Create an LRU cache with a maximum size of 100 items
const lruCache = new LRUCache<string, number>({ maxSize: 100 });

// Adding entries to the cache
lruCache.set("key1", 1);
lruCache.set("key2", 2);

// Retrieving a value from the cache
const value = lruCache.get("key1").FromMaybe(0); // Returns 1
```

#### LFU Cache Example

```typescript
import { LFUCache } from "@lnear/cache";

// Create an LFU cache with a maximum size of 100 items
const lfuCache = new LFUCache<string, number>({ maxSize: 100 });

// Adding entries to the cache
lfuCache.set("key1", 1);
lfuCache.set("key2", 2);

// Retrieving a value from the cache
const value = lfuCache.get("key1").FromMaybe(0); // Returns 1
```

### Contributing

I welcome contributions to `@lnear/cache` to report bugs, suggest improvements, or add new features. Check out the [GitHub repository](https://github.com/lnear-dev/cache) for more details on how to contribute.

### Learn More

For detailed documentation and further examples, visit the [official repository](https://github.com/lnear-dev/cache).
Happy caching with `@lnear/cache`!
