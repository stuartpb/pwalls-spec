# proot-spec

HTML/CSS/DOM Parts and Roots Proposal

## Status

This document is "pre-specification" which means that I haven't even figured out enough spec bureaucracy to give this document a proper status.

## Overview

This document proposes two new attributes that, like `class` and `id`, may be applied to any element in HTML:

- `root`, a boolean attribute specifying that the element is a pseudo-root, herefore shortened as "proot" (another potential name for this attribute if `root` is deemed to be too broad/conflict-prone for a global attribute name).
- `part`, an attribute with a name value, following the same uniqueness and traversal constraints within proot boundaries as `id` has within document boundaries.
  - `id` may still be defined on an element with `part`, as they have different access implications. (A framework or polyfill may generate `id` values to uniquely identify every element in a document: this will not affect `part`.)

## Motivation

In short: because Shadow DOM is too complicated, and Custom Elements is *way* too complicated. This spec doesn't *replace* Shadow DOM, it just makes it so you don't have to use it just so you can have *basic* encapsulation. (Indeed, this new approach *also works within* Shadow DOM.)

It provides a facility page authors may use to create a hierarchy *within* the context of their page, allowing them to have simple and strightforward access *within* those hierarchies (as opposed to approaches like the failed `/deep/` selector for styling within Shadow DOM).

## DOM Parts

Elements with `root` defined (and Shadow DOM roots) have a `getPart()` function, which works similarly to how `getElementById` works, except looking for `part` rather than `id` and avoiding traversal beyond proot boundaries as well as document boundaries.

(Defining this on Shadow DOM roots may mean it's simpler to also define these on the root `document` for a page.)

Alternately, elements may have a `parts` object with accessors for each, similar to `dataset` or `window`. (I honestly think UAs should provide both - I feel there are enough specs forcing authors to use their opinion-of-the-week access pattern that doesn't match the rest of what the author is working with.)

## Part Boundary Selector

A `/` character may be used after a selector specifying a element, ID, and/or class, to define a proot or Shadow DOM boundary crossing. Selectors to the right of the `/` may only specify elements, classes, or part or ID names: the `#` character, after a `/`, refers to either IDs or parts within that DOM / proot boundary. It *must not cross* further proot boundaries.

Note: It is important to note that the part *left of any slashes* may - like all other selectors - traverse *any number* of proot elements. The proot boundaries *only* come into play *around the slash*. Any non-slash descendant selectors (namely, the whitespace "general descendant" selector) *do not* follow rules regarding proot boundaries, *even after a slash*.

## Differences between proot and Shadow DOM

- Elements within a proot are still selected by selectors in the top-level document, as well as functions like `getElementsByClassName`.
- proots can be defined without JS.

## Differences between part and class

- Parts are expected to be unique within the context of their proot. Classes have no such restriction.
- Unlike classes, parts are not found beneath a proot boundary.

## Extensions to existing elements

A standard structure for the standard elements UAs currently construct within Shadow DOM could be defined, using `part` attributes to refer to a hierarchy of their components, for access in styling, as an alternative to non-standard vendor-prefixed pseudo-elements, as we currently use. (This includes the sub-components of scrollbars, which would remain pseudo-elements, but be standardized as pseudo-elements containing proots.)

## Example: Doctor Listings 

```html
<html>
<head>
<style>
/* TODO: finish example stylesheet */
.profile/#photo {

}
.profile/#name {

}
.profile/#bio {

}
</style>
</head>
<body>
<div id="profiles">
  <div class="profile" root>
    <img src="person-a.jpg" part="avatar">
    <div part="name">Foo Bar</div>
    <p part="bio">lorem ipsum</p>
  </div>
  <div class="profile" root>
    <img src="person-b.jpg" part="avatar">
    <div part="name">Boo Car</div>
    <p part="bio">lorem ipsum</p>
  </div>
  <div class="profile" root>
    <img src="person-c.jpg" part="avatar">
    <div part="name">Kenny Baz</div>
    <p part="bio">lorem ipsum</p>
  </div>
</div>
</body>
</html>
```

## Example: Chat UI (initial content with HTML template)

```html
<html>
<head>
<style>
/* TODO: finish example stylesheet */
.message-card/#avatar {

}
.message-card/#name {

}
.message-card/#message {

}
</style>
<template id="message-card-template">
  <div class="message-card" root>
    <img part="avatar">
    <div part="name"></div>
    <div part="message"></div>
  </div>
</template>
</head>
<body>
<div id="messages"></div>
<script>
// TODO: write script
</script>
</body>
</html>
```

## Example: Chat UI (live snapshot of variant using no HTML template)

```html
<html>
<head>
<link rel="stylesheet" href="same-style-as-last-template.css">
</head>
<body>
<div id="messages">
  <div class="message-card" id="7cf00c89-800f-495b-9e44-a2ffd9df817c" root>
  </div>
  <div class="message-card" id="26888874-dbe9-4efc-8f88-873af7139a4d" root>
  </div>
  <div class="message-card" id="5c212dc7-5d73-4759-a466-589a08d5a98e" root>
  </div>
  <div class="message-card" id="ed8818ff-353d-4219-a28f-a97b531e95f2" root>
  </div>
  <div class="message-card" id="331bb13b-7071-4d69-9b04-fb62502a5601" root>
  </div>
  <div class="message-card" id="7fc6ee0c-85d2-47e2-a8f4-4f499d44bfdb" root>
  </div>
  <div class="message-card" id="95ca156f-8e59-4372-8e6f-bff9c8a7c4ac" root>
  </div>
  <div class="message-card" id="cd3ac0ae-925f-45e8-a1e7-98e1b215ba9e" root>
  </div>
</div>
<script>
// TODO: write script
</script>
</body>
</html>
```

## TODO

- Everything marked "TODO" above.
- Add example with more complex components, with an example using part names not traversing proot boundaries (which is largely the point of having `part` and not just using `class` for this stuff).
- Add proposals for what the standard element parts look like.
