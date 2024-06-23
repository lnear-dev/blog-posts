---
title: "Understanding RAII in PHP: A Practical Example"
slug: understanding-raii-in-php-a-practical-example
description: "<p>Resource Acquisition Is Initialization (RAII) is a programming idiom primarily associated with C++ but also applicable to other languages, including PHP. RAII simplifies resource management by tying resource lifecycle to object lifetime, ensuring that resources are properly released when no longer needed. In this blog post, we’ll explore RAII in...</p>\n"
date: "2023-12-29"
---

# Understanding RAII in PHP: A Practical Example

Resource Acquisition Is Initialization (RAII) is a programming idiom primarily associated with C++ but also applicable to other languages, including PHP. RAII simplifies resource management by tying resource lifecycle to object lifetime, ensuring that resources are properly released when no longer needed. In this blog post, we’ll explore RAII in PHP through a practical example using a custom `Valuestore` class.

## What is RAII?

RAII is a programming technique where resources are acquired and released by objects. In RAII, resource acquisition (like memory allocation, file opening, etc.) happens during object creation (initialization), and resource release (cleanup) occurs during object destruction (finalization). This ensures that resources are managed efficiently and safely, preventing resource leaks.

### Key Concepts of RAII:

-   **Initialization**: Resources are acquired when an object is created.
-   **Finalization**: Resources are released when the object goes out of scope or is explicitly destroyed.

## Implementing RAII in PHP

PHP is a garbage-collected language, meaning it automatically handles memory management. However, managing other resources (like file handles or database connections) can benefit from RAII principles. Let’s look at how RAII can be implemented in PHP using a `Valuestore` class that manages a JSON file.

### The `Valuestore` Class

The `Valuestore` class, part of the `Lame\Valuestore` namespace, provides a convenient way to store and retrieve values from a JSON file. Here is the complete class implementation:

```php
namespace Lame\Valuestore;

use ArrayAccess;
use Countable;
use InvalidArgumentException;
use JsonException;
use RuntimeException;

class Valuestore implements ArrayAccess, Countable
{
    protected string $fileName;
    protected array $data;

    public static function make(string $fileName, ?array $values = null): static
    {
        $valuestore = new static($fileName);

        if (!is_null($values)) {
            $valuestore->put($values);
        }

        return $valuestore;
    }

    public function __construct(string $fileName)
    {
        $this->setFileName($fileName);
        $this->data = $this->load();
    }

    public function __destruct()
    {
        $this->save();
    }

    protected function setFileName(string $fileName): static
    {
        $this->fileName = $fileName;

        return $this;
    }

    public function put(array|string $name, mixed $value = null): static
    {
        if ($name === []) {
            return $this;
        }

        $newValues = is_array($name) ? $name : [$name => $value];
        $this->data = array_merge($this->data, $newValues);

        return $this;
    }

    public function push(string $name, mixed $pushValue): static
    {
        $pushValue = (array)$pushValue;

        $oldValue = $this->get($name, []);

        if (!is_array($oldValue)) {
            $oldValue = [$oldValue];
        }

        $newValue = array_merge($oldValue, $pushValue);

        return $this->put($name, $newValue);
    }

    public function prepend(string $name, mixed $prependValue): static
    {
        $prependValue = (array)$prependValue;

        $oldValue = $this->get($name, []);

        if (!is_array($oldValue)) {
            $oldValue = [$oldValue];
        }

        $newValue = array_merge($prependValue, $oldValue);

        return $this->put($name, $newValue);
    }

    public function get(string $name, mixed $default = null): mixed
    {
        return $this->data[$name] ?? $default;
    }

    public function has(string $name): bool
    {
        return array_key_exists($name, $this->data);
    }

    public function all(): array
    {
        return $this->data;
    }

    public function allStartingWith(string $startingWith = ''): array
    {
        return $this->filterKeysStartingWith($this->data, $startingWith);
    }

    public function forget(string $key): static
    {
        unset($this->data[$key]);

        return $this;
    }

    public function flush(): static
    {
        $this->data = [];

        return $this;
    }

    public function flushStartingWith(string $startingWith = ''): static
    {
        if ($startingWith === '') {
            return $this->flush();
        }

        $this->data = $this->filterKeysNotStartingWith($this->data, $startingWith);

        return $this;
    }

    public function pull(string $name): mixed
    {
        $value = $this->get($name);
        $this->forget($name);

        return $value;
    }

    public function increment(string $name, int $by = 1): mixed
    {
        $currentValue = $this->get($name, 0);

        if (!$this->isNumber($currentValue)) {
            throw new InvalidArgumentException("The value for '{$name}' is not a number.");
        }

        $newValue = $currentValue + $by;
        $this->put($name, $newValue);

        return $newValue;
    }

    public function decrement(string $name, int $by = 1): mixed
    {
        return $this->increment($name, -$by);
    }

    public function offsetExists($offset): bool
    {
        return $this->has($offset);
    }

    public function offsetGet($offset): mixed
    {
        return $this->get($offset);
    }

    public function offsetSet($offset, $value): void
    {
        $this->put($offset, $value);
    }

    public function offsetUnset($offset): void
    {
        $this->forget($offset);
    }

    public function count(): int
    {
        return count($this->data);
    }

    protected function filterKeysStartingWith(array $values, string $startsWith): array
    {
        return array_filter($values, fn($key) => str_starts_with($key, $startsWith), ARRAY_FILTER_USE_KEY);
    }

    protected function filterKeysNotStartingWith(array $values, string $startsWith): array
    {
        return array_filter($values, fn($key) => !str_starts_with($key, $startsWith), ARRAY_FILTER_USE_KEY);
    }

    protected function load(): array
    {
        if (!file_exists($this->fileName)) {
            return [];
        }

        try {
            $content = file_get_contents($this->fileName);
            return json_decode($content, true, 512, JSON_THROW_ON_ERROR);
        } catch (JsonException | InvalidArgumentException $e) {
            throw new RuntimeException("Failed to load data from {$this->fileName}: " . $e->getMessage());
        }
    }

    protected function save(): void
    {
        if (empty($this->data)) {
            @unlink($this->fileName);
        } else {
            try {
                file_put_contents($this->fileName, json_encode($this->data, JSON_THROW_ON_ERROR));
            } catch (JsonException $e) {
                throw new RuntimeException("Failed to save data to {$this->fileName}: " . $e->getMessage());
            }
        }
    }

    protected function isNumber($value): bool
    {
        return is_int($value) || is_float($value);
    }
}
```

### Applying RAII in the `Valuestore` Class

In this class, the RAII principle is demonstrated through the constructor and destructor:

#### Constructor: Resource Acquisition

```php
public function __construct(string $fileName)
{
    $this->setFileName($fileName);
    $this->data = $this->load();
}
```

When a `Valuestore` object is created, the constructor sets the filename and loads the data from the JSON file. This ensures that the resource (file data) is acquired and initialized as soon as the object is instantiated.

#### Destructor: Resource Release

```php
public function __destruct()
{
    $this->save();
}
```

The destructor is called when the object is destroyed. It saves the current state of the data back to the JSON file. This ensures that any changes made to the data are persisted, thereby releasing the resource appropriately.

### Benefits of RAII in `Valuestore`

-   **Automatic Resource Management**: The data is automatically loaded when the object is created and saved when the object is destroyed.
-   **Exception Safety**: If an exception occurs, the destructor still gets called, ensuring that resources are properly released.
-   **Simplified Code**: Developers do not need to explicitly call load and save methods; they are handled by the constructor and destructor.

## Conclusion

RAII is a powerful concept for managing resources efficiently and safely. Leveraging RAII in PHP ensures that resources are properly acquired and released, leading to more robust and maintainable code. The `Valuestore` class is a practical example of how RAII can be implemented in PHP to manage file-based data storage effectively. With it, you can achieve cleaner and more reliable code by tying resource management to object lifecycle.
