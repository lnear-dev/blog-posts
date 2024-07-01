---
title: 'lnear/html, a PHP library for generating HTML elements'
description: 'lnear/html, a PHP library for generating HTML elements'
date: 2024-07-01T02:46:47.020731+00:00
---
# Generating PHP Code for HTML Elements: My Journey

Embarking on the journey to create a PHP library for generating HTML elements was both challenging and rewarding. This project aimed to dynamically generate PHP functions for all HTML elements and their attributes, ensuring they were up-to-date with the latest HTML specifications. Here, I’ll detail the process, the challenges faced, and how I overcame them to achieve the final product.

## Setting Up the Environment

### Initial Setup

To start, I set up the environment with necessary dependencies and constants. The script requires the `vendor/autoload.php` for loading Composer dependencies and uses Symfony’s Console component for the command-line interface.

```php
#!/usr/bin/env php
<?php
require __DIR__ . '/vendor/autoload.php';
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\SingleCommandApplication;

const kSpec             = 'https://html.spec.whatwg.org/multipage/indices.html';
const kJSONOutFile      = __DIR__ . '/src/elements.json';
const kPHPOutFile       = __DIR__ . '/src/elements.php';
const kGlobalAttributes = [
    'accesskey', 'autocapitalize', 'class', 'contenteditable', 'contextmenu', 'dir',
    'draggable', 'dropzone', 'hidden', 'id', 'itemid', 'itemprop', 'itemref',
    'itemscope', 'itemtype', 'lang', 'spellcheck', 'style', 'tabindex', 'title', 'translate',
];
```

These constants define the URL for the HTML specification, the output paths for JSON and PHP files, and a list of global HTML attributes.

## Core Functionality

### Fetching and Parsing the HTML Specification

The primary task was to fetch and parse the HTML specification to extract the list of HTML elements and their attributes. This was achieved using `DOMDocument` and `DOMXPath` in PHP.

```php
class Bin
{
    static function update()
    {
        $html = file_get_contents(kSpec);
        if ($html === false) {
            die("Failed to fetch HTML content.");
        }

        $dom = new DOMDocument();
        libxml_use_internal_errors(true);
        $dom->loadHTML($html);
        libxml_clear_errors();

        $xpath = new DOMXPath($dom);
        $elements = [];
        $elements_query = $xpath->query('//table[caption[text()="List of elements"]]/tbody/tr');

        foreach ($elements_query as $element) {
            if (
                !($tag = $xpath->query('th/code/a', $element)->item(0)?->textContent)
                && !($tag = $xpath->query('th/a/code', $element)->item(0)?->textContent)
            ) {
                continue;
            }

            $attributes_node = $xpath->query('td[5]', $element)->item(0);
            $attributes = [];
            if ($attributes_node) {
                foreach ($xpath->query('code/a', $attributes_node) as $attr) {
                    $attributes[] = $attr->textContent;
                }
            }

            $children = $xpath->query('td[4]', $element)->item(0)->textContent;
            $selfClosing = strpos($children, 'empty') !== false;

            $e = ["tag" => $tag];
            if (!empty($attributes))
                $e["attributes"] = $attributes;
            if ($selfClosing)
                $e["selfClosing"] = true;
            $elements[] = $e;
        }

        file_put_contents(kJSONOutFile, json_encode($elements, JSON_PRETTY_PRINT));
        self::generate($elements);
    }

    ...
}
```

This method fetches the HTML specification, parses it using `DOMDocument` and `DOMXPath`, and extracts the relevant data, which is then saved to a JSON file.

### Generating PHP Code

With the list of elements and their attributes extracted, the next step was to generate the corresponding PHP functions. Each function represents an HTML tag and its attributes.

```php
    static function camelArg(string $string): string
    {
        return "\$" . lcfirst(str_replace(' ', '', ucwords(str_replace('-', ' ', $string))));
    }

    static function generateElementCode(?string $tag, array $attributes, bool $selfClosing, int $level = 1): string
    {
        $fname = $tag;
        if ($tag === 'var') {
            $fname = 'variable';
        }

        if (!$selfClosing) {
            array_unshift($attributes, 'body');
        }

        $attributes_impl = join(
            ".\n",
            array_map(
                function ($attr) use ($level) {
                    $arg = self::camelArg($attr);
                    return self::indent("(\${$arg} !== '' ? \" {$attr}='\" . htmlspecialchars(\${$arg}, ENT_QUOTES) . \"'\" : '')", $level);
                },
                $attributes,
            ),
        );

        $attributes_impl = $attributes_impl ? $attributes_impl . '.' : '';
        $params          = self::indent(
            join(
                ",\n",
                array_map(
                    fn($attr) => "string " . self::camelArg($attr) . '= ""',
                    $attributes,
                ),
            ),
            $level,
        );

        $body = self::indent(
            "return \"<{$tag}\" . {$attributes_impl} " . ($selfClosing ? '"/>"' : "\">{\$body}</{$tag}>\"") . ";\n",
            $level,
        );

        return "function {$fname}(\n{$params}\n) {\n{$body}\n}";
    }

    static function lint(): void
    {
        passthru("php -l " . kPHPOutFile);
        passthru("php-cs-fixer fix --config .php-cs-fixer.php");
    }

    static function generate(array $elements): void
    {
        file_put_contents(kPHPOutFile, '');

        $file = fopen(kPHPOutFile, 'w');
        if ($file === false) {
            die("Failed to open PHP file for writing.");
        }

        fwrite($file, "<?php\n\nnamespace html;\n\n");

        foreach ($elements as $element) {
            $tag = $element['tag'];
            $attributes = array_merge($element['attributes'] ?? [], kGlobalAttributes);
            $selfClosing = $element['selfClosing'] ?? false;
            fwrite($file, self::generateElementCode($tag, $attributes, $selfClosing) . "\n\n");
        }

        fclose($file);
        self::lint();
        self::generateReadme($elements);
    }

    static function indent(string $string, int $level = 1): string
    {
        return join(
            "\n",
            array_map(
                fn($line) => str_repeat('    ', $level) . $line,
                explode("\n", $string),
            ),
        );
    }
```

The `generateElementCode` function creates PHP code for each HTML element, handling both self-closing and regular tags. The `generate` function writes these functions to the `elements.php` file.

### Linting and Generating the README

To ensure code quality, I included linting and formatting steps using `php -l` and `php-cs-fixer`.

```php
    static function lint(): void
    {
        passthru("php -l " . kPHPOutFile);
        passthru("php-cs-fixer fix --config .php-cs-fixer.php");
    }
```

Additionally, a README file was generated to document the usage of the library.

```php
    static function generateReadme(array $elements)
    {
        $readme = fopen(__DIR__ . '/README.md', 'w');
        if ($readme === false)
            die("Failed to open README file for writing.");

        $globalAttributes = join("\n", array_map(fn($attribute) => "| `{$attribute}` |", kGlobalAttributes));
        $noAtt            = array_filter($elements, fn($element) => count($element['attributes'] ?? []) === 0);
        $elements         = array_filter($elements, fn($element) => !empty ($element['attributes'] ?? []));
        $noAtt            = join("\n", array_map(fn($element) => "| `{$element['tag']}` |", $noAtt));
        $elements         = join("\n", array_map(fn($element) => "| `{$element['tag']}` | " . join(', ', $element['attributes']) . " |", $elements));

        $contents = <<<MARKDOWN
# lnear/html, a PHP library for generating HTML elements

...

All elements share the following global attributes:

| Attribute |
| --------- |
{$globalAttributes}

The following elements only have the global attributes:

| Tag |
| --- |
{$noAtt}

The following elements have additional attributes:

| Tag | Attributes |
| --- | ---------- |
{$elements}

MARKDOWN;
        fwrite($readme, $contents);
        fclose($readme);
    }

```

### Command-Line Interface

Finally, I tied everything together with a command-line interface using Symfony’s Console component. This CLI allows updating the elements list from the specification or generating the PHP code directly from the JSON file.

```php
(new SingleCommandApplication())
    ->setName('Generate HTML Elements')
    ->setVersion('1.0.0')
    ->addOption('update', 'u', InputOption::VALUE_NONE, 'Update the elements list from the HTML specification')
    ->setCode(function (InputInterface $input, OutputInterface $output): int {
        if ($input->getOption('update')) {
            Bin::update();
            $output->writeln('Elements list updated.');
        } else {
            Bin::generate(json_decode(file_get_contents(kJSONOutFile), true));
        }
        return 0;
    })
    ->run();
```

## Conclusion

Building this PHP library for generating HTML elements was a comprehensive project that involved parsing the HTML specification, dynamically generating PHP code, ensuring code quality, and creating detailed documentation. This journey deepened my understanding of PHP and HTML, and enhanced my skills in automation and code generation.

Feel free to explore the library, contribute, and provide feedback. Happy coding!
