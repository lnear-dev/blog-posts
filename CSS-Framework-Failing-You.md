---
title: "Why Your CSS Framework is Failing You"
slug: why-your-css-framework-is-failing-you
description: "<p>CSS frameworks like Bootstrap, Tailwind CSS, and Foundation have gained popularity for their pre-designed components and utility classes that simplify web development. These frameworks offer a wide range of styles and layouts to create responsive and visually appealing websites quickly. However, relying solely on CSS frameworks can lead to several issues that may hinder...</p>\n"
date: "2023-12-24"
---

# Why Your CSS Framework is Failing You

CSS frameworks like Bootstrap, Tailwind CSS, and Foundation have gained popularity for their pre-designed components and utility classes that simplify web development. These frameworks offer a wide range of styles and layouts to create responsive and visually appealing websites quickly. However, relying solely on CSS frameworks can lead to several issues that may hinder your development process and affect the performance and maintainability of your code. In this article, we'll explore why your CSS framework might be failing you and how you can address these challenges to improve your web development workflow.

We'll focus specifically on Tailwind CSS, the most popular utility-first CSS framework. At first glance, Tailwind seems like a godsend—a utility-first CSS framework that simplifies the styling process. However, as we dig deeper, it becomes clear that Tailwind might be doing more harm than good, particularly when it comes to the size of your CSS files.

## Example Comparison

Let's take a look at an example right from the [Tailwind CSS documentation](https://tailwindcss.com/docs/utility-first).

### Traditional CSS Approach

```html
<div class="chat-notification">
    <div class="chat-notification-logo-wrapper">
        <img
            class="chat-notification-logo"
            src="/img/logo.svg"
            alt="ChitChat Logo"
        />
    </div>
    <div class="chat-notification-content">
        <h4 class="chat-notification-title">ChitChat</h4>
        <p class="chat-notification-message">You have a new message!</p>
    </div>
</div>

<style>
    .chat-notification {
        display: flex;
        align-items: center;
        max-width: 24rem;
        margin: 0 auto;
        padding: 1.5rem;
        border-radius: 0.5rem;
        background-color: #fff;
        box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
    }
    .chat-notification-logo-wrapper {
        flex-shrink: 0;
    }
    .chat-notification-logo {
        height: 3rem;
        width: 3rem;
    }
    .chat-notification-content {
        margin-left: 1.5rem;
    }
    .chat-notification-title {
        color: #1a202c;
        font-size: 1.25rem;
        line-height: 1.25;
    }
    .chat-notification-message {
        color: #718096;
        font-size: 1rem;
        line-height: 1.5;
    }
</style>
```

### Tailwind CSS Approach

```html
<div
    class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex items-center space-x-4"
>
    <div class="shrink-0">
        <img class="h-12 w-12" src="/img/logo.svg" alt="ChitChat Logo" />
    </div>
    <div>
        <div class="text-xl font-medium text-black">ChitChat</div>
        <p class="text-slate-500">You have a new message!</p>
    </div>
</div>
```

## The Problem with Utility Classes

While the Tailwind CSS approach is concise and readable, the other examples are more self-explanatory and don't require you to know the utility classes to understand what's going on. They are also self-contained, meaning you don't have to jump around the codebase to find the styles applied to a particular element, and no additional CSS file is required to style the component.

The Tailwind example, on the other hand, requires you to know the utility classes to understand the styling. If you want to modify the styles, you have to jump around the codebase to find the utility classes and update them accordingly, or use [Multi-Cursor Editing](https://tailwindcss.com/docs/reusing-styles#multi-cursor-editing) as Tailwind suggests.

Moreover, the Tailwind example won't work out of the box. It needs to generate the following CSS:

[From Tailwind's Playground](https://play.tailwindcss.com/)

```css
/*
! tailwindcss v3.4.4 | MIT License | https://tailwindcss.com
*/
/*
1. Prevent padding and border from affecting element width. (https://github.com/mozdevs/cssremedy/issues/4)
2. Allow adding a border to an element by just adding a border-width. (https://github.com/tailwindcss/tailwindcss/pull/116)
*/
*,
::before,
::after {
    box-sizing: border-box;
    /* 1 */
    border-width: 0;
    /* 2 */
    border-style: solid;
    /* 2 */
    border-color: #e5e7eb;
    /* 2 */
}
::before,
::after {
    --tw-content: "";
}
/*
1. Use a consistent sensible line-height in all browsers.
2. Prevent adjustments of font size after orientation changes in iOS.
3. Use a more readable tab size.
4. Use the user's configured `sans` font-family by default.
5. Use the user's configured `sans` font-feature-settings by default.
6. Use the user's configured `sans` font-variation-settings by default.
7. Disable tap highlights on iOS
*/
html,
:host {
    line-height: 1.5;
    /* 1 */
    -webkit-text-size-adjust: 100%;
    /* 2 */
    -moz-tab-size: 4;
    /* 3 */
    tab-size: 4;
    /* 3 */
    font-family: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji",
        "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
    /* 4 */
    font-feature-settings: normal;
    /* 5 */
    font-variation-settings: normal;
    /* 6 */
    -webkit-tap-highlight-color: transparent;
    /* 7 */
}
/*
1. Remove the margin in all browsers.
2. Inherit line-height from `html` so users can set them as a class directly on the `html` element.
*/
body {
    margin: 0;
    /* 1 */
    line-height: inherit;
    /* 2 */
}
/*
1. Add the correct height in Firefox.
2. Correct the inheritance of border color in Firefox. (https://bugzilla.mozilla.org/show_bug.cgi?id=190655)
3. Ensure horizontal rules are visible by default.
*/
hr {
    height: 0;
    /* 1 */
    color: inherit;
    /* 2 */
    border-top-width: 1px;
    /* 3 */
}
/*
Add the correct text decoration in Chrome, Edge, and Safari.
*/
abbr:where([title]) {
    text-decoration: underline dotted;
}
/*
Remove the default font size and weight for headings.
*/
h1,
h2,
h3,
h4,
h5,
h6 {
    font-size: inherit;
    font-weight: inherit;
}
/*
Reset links to optimize for opt-in styling instead of opt-out.
*/
a {
    color: inherit;
    text-decoration: inherit;
}
/*
Add the correct font weight in Edge and Safari.
*/
b,
strong {
    font-weight: bolder;
}
/*
1. Use the user's configured `mono` font-family by default.
2. Use the user's configured `mono` font-feature-settings by default.
3. Use the user's configured `mono` font-variation-settings by default.
4. Correct the odd `em` font sizing in all browsers.
*/
code,
kbd,
samp,
pre {
    font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
        "Liberation Mono", "Courier New", monospace;
    /* 1 */
    font-feature-settings: normal;
    /* 2 */
    font-variation-settings: normal;
    /* 3 */
    font-size: 1em;
    /* 4 */
}
/*
Add the correct font size in all browsers.
*/
small {
    font-size: 80%;
}
/*
Prevent `sub` and `sup` elements from affecting the line height in all browsers.
*/
sub,
sup {
    font-size: 75%;
    line-height: 0;
    position: relative;
    vertical-align: baseline;
}
sub {
    bottom: -0.25em;
}
sup {
    top: -0.5em;
}
/*
1. Remove text indentation from table contents in Chrome and Safari. (https://bugs.chromium.org/p/chromium/issues/detail?id=999088, https://bugs.webkit.org/show_bug.cgi?id=201297)
2. Correct table border color inheritance in all Chrome and Safari. (https://bugs.chromium.org/p/chromium/issues/detail?id=935729, https://bugs.webkit.org/show_bug.cgi?id=195016)
3. Remove gaps between table borders by default.
*/
table {
    text-indent: 0;
    /* 1 */
    border-color: inherit;
    /* 2 */
    border-collapse: collapse;
    /* 3 */
}
/*
1. Change the font styles in all browsers.
2. Remove the margin in Firefox and Safari.
3. Remove default padding in all browsers.
*/
button,
input,
optgroup,
select,
textarea {
    font-family: inherit;
    /* 1 */
    font-feature-settings: inherit;
    /* 1 */
    font-variation-settings: inherit;
    /* 1 */
    font-size: 100%;
    /* 1 */
    font-weight: inherit;
    /* 1 */
    line-height: inherit;
    /* 1 */
    letter-spacing: inherit;
    /* 1 */
    color: inherit;
    /* 1 */
    margin: 0;
    /* 2 */
    padding: 0;
    /* 3 */
}
/*
Remove the inheritance of text transform in Edge and Firefox.
*/
button,
select {
    text-transform: none;
}
/*
1. Correct the inability to style clickable types in iOS and Safari.
2. Remove default button styles.
*/
button,
input:where([type="button"]),
input:where([type="reset"]),
input:where([type="submit"]) {
    -webkit-appearance: button;
    /* 1 */
    background-color: transparent;
    /* 2 */
    background-image: none;
    /* 2 */
}
/*
Use the modern Firefox focus style for all focusable elements.
*/
:-moz-focusring {
    outline: auto;
}
/*
Remove the additional `:invalid` styles in Firefox. (https://github.com/mozilla/gecko-dev/blob/2f9eacd9d3d995c937b4251a5557d95d494c9be1/layout/style/res/forms.css#L728-L737)
*/
:-moz-ui-invalid {
    box-shadow: none;
}
/*
Add the correct vertical alignment in Chrome and Firefox.
*/
progress {
    vertical-align: baseline;
}
/*
Correct the cursor style of increment and decrement buttons in Safari.
*/
::-webkit-inner-spin-button,
::-webkit-outer-spin-button {
    height: auto;
}
/*
1. Correct the odd appearance in Chrome and Safari.
2. Correct the outline style in Safari.
*/
[type="search"] {
    -webkit-appearance: textfield;
    /* 1 */
    outline-offset: -2px;
    /* 2 */
}
/*
Remove the inner padding in Chrome and Safari on macOS.
*/
::-webkit-search-decoration {
    -webkit-appearance: none;
}
/*
1. Correct the inability to style clickable types in iOS and Safari.
2. Change font properties to `inherit` in Safari.
*/
::-webkit-file-upload-button {
    -webkit-appearance: button;
    /* 1 */
    font: inherit;
    /* 2 */
}
/*
Add the correct display in Chrome and Safari.
*/
summary {
    display: list-item;
}
/*
Removes the default spacing and border for appropriate elements.
*/
blockquote,
dl,
dd,
h1,
h2,
h3,
h4,
h5,
h6,
hr,
figure,
p,
pre {
    margin: 0;
}
fieldset {
    margin: 0;
    padding: 0;
}
legend {
    padding: 0;
}
ol,
ul,
menu {
    list-style: none;
    margin: 0;
    padding: 0;
}
/*
Reset default styling for dialogs.
*/
dialog {
    padding: 0;
}
/*
Prevent resizing textareas horizontally by default.
*/
textarea {
    resize: vertical;
}
/*
1. Reset the default placeholder opacity in Firefox. (https://github.com/tailwindlabs/tailwindcss/issues/3300)
2. Set the default placeholder color to the user's configured gray 400 color.
*/
input::placeholder,
textarea::placeholder {
    opacity: 1;
    /* 1 */
    color: #9ca3af;
    /* 2 */
}
/*
Set the default cursor for buttons.
*/
button,
[role="button"] {
    cursor: pointer;
}
/*
Make sure disabled buttons don't get the pointer cursor.
*/
:disabled {
    cursor: default;
}
/*
1. Make replaced elements `display: block` by default. (https://github.com/mozdevs/cssremedy/issues/14)
2. Add `vertical-align: middle` to align replaced elements more sensibly by default. (https://github.com/jensimmons/cssremedy/issues/14#issuecomment-634934210)
   This can trigger a poorly considered lint error in some tools but is included by design.
*/
img,
svg,
video,
canvas,
audio,
iframe,
embed,
object {
    display: block;
    /* 1 */
    vertical-align: middle;
    /* 2 */
}
/*
Constrain images and videos to the parent width and preserve their intrinsic aspect ratio. (https://github.com/mozdevs/cssremedy/issues/14)
*/
img,
video {
    max-width: 100%;
    height: auto;
}
/* Make elements with the HTML hidden attribute stay hidden by default */
[hidden] {
    display: none;
}
*,
::before,
::after {
    --tw-border-spacing-x: 0;
    --tw-border-spacing-y: 0;
    --tw-translate-x: 0;
    --tw-translate-y: 0;
    --tw-rotate: 0;
    --tw-skew-x: 0;
    --tw-skew-y: 0;
    --tw-scale-x: 1;
    --tw-scale-y: 1;
    --tw-pan-x: ;
    --tw-pan-y: ;
    --tw-pinch-zoom: ;
    --tw-scroll-snap-strictness: proximity;
    --tw-gradient-from-position: ;
    --tw-gradient-via-position: ;
    --tw-gradient-to-position: ;
    --tw-ordinal: ;
    --tw-slashed-zero: ;
    --tw-numeric-figure: ;
    --tw-numeric-spacing: ;
    --tw-numeric-fraction: ;
    --tw-ring-inset: ;
    --tw-ring-offset-width: 0px;
    --tw-ring-offset-color: #fff;
    --tw-ring-color: rgb(59 130 246 / 0.5);
    --tw-ring-offset-shadow: 0 0 #0000;
    --tw-ring-shadow: 0 0 #0000;
    --tw-shadow: 0 0 #0000;
    --tw-shadow-colored: 0 0 #0000;
    --tw-blur: ;
    --tw-brightness: ;
    --tw-contrast: ;
    --tw-grayscale: ;
    --tw-hue-rotate: ;
    --tw-invert: ;
    --tw-saturate: ;
    --tw-sepia: ;
    --tw-drop-shadow: ;
    --tw-backdrop-blur: ;
    --tw-backdrop-brightness: ;
    --tw-backdrop-contrast: ;
    --tw-backdrop-grayscale: ;
    --tw-backdrop-hue-rotate: ;
    --tw-backdrop-invert: ;
    --tw-backdrop-opacity: ;
    --tw-backdrop-saturate: ;
    --tw-backdrop-sepia: ;
    --tw-contain-size: ;
    --tw-contain-layout: ;
    --tw-contain-paint: ;
    --tw-contain-style: ;
}
::backdrop {
    --tw-border-spacing-x: 0;
    --tw-border-spacing-y: 0;
    --tw-translate-x: 0;
    --tw-translate-y: 0;
    --tw-rotate: 0;
    --tw-skew-x: 0;
    --tw-skew-y: 0;
    --tw-scale-x: 1;
    --tw-scale-y: 1;
    --tw-pan-x: ;
    --tw-pan-y: ;
    --tw-pinch-zoom: ;
    --tw-scroll-snap-strictness: proximity;
    --tw-gradient-from-position: ;
    --tw-gradient-via-position: ;
    --tw-gradient-to-position: ;
    --tw-ordinal: ;
    --tw-slashed-zero: ;
    --tw-numeric-figure: ;
    --tw-numeric-spacing: ;
    --tw-numeric-fraction: ;
    --tw-ring-inset: ;
    --tw-ring-offset-width: 0px;
    --tw-ring-offset-color: #fff;
    --tw-ring-color: rgb(59 130 246 / 0.5);
    --tw-ring-offset-shadow: 0 0 #0000;
    --tw-ring-shadow: 0 0 #0000;
    --tw-shadow: 0 0 #0000;
    --tw-shadow-colored: 0 0 #0000;
    --tw-blur: ;
    --tw-brightness: ;
    --tw-contrast: ;
    --tw-grayscale: ;
    --tw-hue-rotate: ;
    --tw-invert: ;
    --tw-saturate: ;
    --tw-sepia: ;
    --tw-drop-shadow: ;
    --tw-backdrop-blur: ;
    --tw-backdrop-brightness: ;
    --tw-backdrop-contrast: ;
    --tw-backdrop-grayscale: ;
    --tw-backdrop-hue-rotate: ;
    --tw-backdrop-invert: ;
    --tw-backdrop-opacity: ;
    --tw-backdrop-saturate: ;
    --tw-backdrop-sepia: ;
    --tw-contain-size: ;
    --tw-contain-layout: ;
    --tw-contain-paint: ;
    --tw-contain-style: ;
}
.mx-auto {
    margin-left: auto;
    margin-right: auto;
}
.flex {
    display: flex;
}
.h-12 {
    height: 3rem;
}
.w-12 {
    width: 3rem;
}
.max-w-sm {
    max-width: 24rem;
}
.shrink-0 {
    flex-shrink: 0;
}
.items-center {
    align-items: center;
}
.space-x-4 > :not([hidden]) ~ :not([hidden]) {
    --tw-space-x-reverse: 0;
    margin-right: calc(1rem * var(--tw-space-x-reverse));
    margin-left: calc(1rem * calc(1 - var(--tw-space-x-reverse)));
}
.rounded-xl {
    border-radius: 0.75rem;
}
.bg-white {
    --tw-bg-opacity: 1;
    background-color: rgb(255 255 255 / var(--tw-bg-opacity));
}
.p-6 {
    padding: 1.5rem;
}
.text-xl {
    font-size: 1.25rem;
    line-height: 1.75rem;
}
.font-medium {
    font-weight: 500;
}
.text-black {
    --tw-text-opacity: 1;
    color: rgb(0 0 0 / var(--tw-text-opacity));
}
.text-slate-500 {
    --tw-text-opacity: 1;
    color: rgb(100 116 139 / var(--tw-text-opacity));
}
.shadow-lg {
    --tw-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 /
                    0.1);
    --tw-shadow-colored: 0 10px 15px -3px var(--tw-shadow-color), 0 4px 6px -4px
            var(--tw-shadow-color);
    box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000), var(
            --tw-ring-shadow,
            0 0 #0000
        ), var(--tw-shadow);
}
```

This is a lot of CSS for a simple component, and for an entire website, the generated CSS can become quite bloated. This can lead to larger CSS files, slower loading times, and increased complexity in your codebase.

## Solution

Use Tailwind CSS for prototyping and building small projects (or not at all), but avoid using it for large-scale projects.

### Learning CSS: A Non-Negotiable Skill

CSS, like any other language, has its nuances and intricacies that require time and practice to master. By using Tailwind as a crutch, developers may avoid learning these critical aspects, leading to several issues:

-   **Limited Problem-Solving Skills:** When unique design challenges arise, developers who haven't fully grasped CSS might struggle to implement custom solutions. Tailwind's predefined classes won't cover every possible scenario, necessitating a deep understanding of CSS to fill in the gaps.
-   **Overly Complex HTML:** Tailwind encourages the use of numerous classes within HTML files, which can make the markup more verbose and harder to read. This complexity can lead to maintainability issues in larger projects.
-   **Performance Issues:** While Tailwind claims to reduce CSS file size by purging unused styles, the initial HTML files can become bloated with utility classes. This bloat can negatively impact performance, particularly on larger applications.

Another significant concern with Tailwind is the risk of over-specialization. Developers might become too reliant on this specific framework, making it difficult to transition to other projects or teams that don't use Tailwind. This can limit career opportunities and professional growth. In contrast, a solid understanding of vanilla CSS makes a developer versatile and adaptable, able to work across various frameworks and environments.

## My Approach

```html
<div class="chat-notification">
    <div>
        <img src="/img/logo.svg" alt="ChitChat Logo" />
    </div>
    <div>
        <h4>ChitChat</h4>
        <p>You have a new message!</p>
    </div>
</div>

<style>
    .chat-notification {
        display: flex;
        align-items: center;
        max-width: 24rem;
        margin: 0 auto;
        padding: 1.5rem;
        border-radius: 0.5rem;
        background-color: #fff;
        box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        > div:first-child {
            flex-shrink: 0;
            > img {
                height: 3rem;
                width: 3rem;
            }
        }
        > div:last-child {
            margin-left: 1.5rem;
            > h4 {
                color: #1a202c;
                font-size: 1.25rem;
                line-height: 1.25;
            }
            > p {
                color: #718096;
                font-size: 1rem;
                line-height: 1.5;
            }
        }
    }
</style>
```

This approach reduces the number of classes and styles applied to the HTML elements, making the code more concise and maintainable. It also allows for better organization and readability of the CSS code, making it easier to update and modify styles in the future and "You aren’t wasting energy inventing class names" as [Tailwind](https://tailwindcss.com/docs/utility-first) puts it.

## Conclusion

While CSS frameworks like Tailwind CSS offer convenience and speed in web development, they come with trade-offs that developers must consider. By understanding the limitations and challenges of relying solely on CSS frameworks, developers can make informed decisions about when and how to use these tools effectively. Ultimately, a solid foundation in CSS is essential for building robust, maintainable, and performant web applications.
