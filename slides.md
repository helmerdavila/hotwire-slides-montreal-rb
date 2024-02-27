---
theme: apple-basic
fonts:
  sans: "Quicksand"
layout: intro-image-right
image: ./images/hotwire.gif
monaco: true
---

# Hotwire - Ruby on Rails
## Intro and usage

---

# Hotwire components

<section style="display: flex; flex-direction: row">
  <img src="/images/turbo.png" alt="Turbo">
  <img src="/images/stimulus.png" alt="Stimulus">
  <img src="/images/strada.png" alt="Strada">
</section>

---

# Turbo components

- _**Turbo Drive**_ accelerates links and form submissions by negating the need for full page reloads.
- _**Turbo Frames**_ decompose pages into independent contexts, which scope navigation and can be lazily loaded.
- _**Turbo Streams**_ deliver page changes over WebSocket, SSE or in response to form submissions using just HTML and a set of CRUD-like actions.
- _**Turbo Native**_ lets your majestic monolith form the center of your native iOS and Android apps, with seamless transitions between web and native sections.

---

# Turbo Drive navigation

Turbo Drive replaces HTML elements in the current page with the corresponding elements from the response, without a full page reload.

```html {monaco-diff}
<html>
  <head>
    <title>My first page</title>
  </head>
  <body>
    <h1>Hello from my first page</h1>
  </body>
</html>
~~~
<html>
  <head>
    <title>My second page</title>
  </head>
  <body>
    <h1>Hello from my second page</h1>
    <h2>And this is a description</h2>
  </body>
</html>
```

---

# Turbo Drive Actions

## As javascript

```js
// Moving forward
Turbo.visit(location)
Turbo.visit(location, { action: "navigate" })
// Replacing top of history
Turbo.visit(location, { action: "replace" })
// Restore (Reserved for internal use, DON'T USE)
Turbo.visit(location, { action: "restore" })
```

## As Ruby (HTML)

```html
<!-- Moving forward -->
<a href="/edit">Edit</a>
<!-- Replacing top of history -->
<a href="/edit" data-turbo-action="replace">Edit</a>
```


---

# Morphing

This is how you can configure page refreshes using Turbo Drive.

```html
<head>
  ...
  <meta name="turbo-refresh-method" content="morph">
</head>
```

There are two options to send as `content`:

- `replace` (default): Replaces the entire body of the page.
- `morph`: Replaces only the elements that have changed.

---

# Scroll preservation

We can also identify how we want to handle the scroll position when navigating.

```html
<head>
  ...
  <meta name="turbo-refresh-scroll" content="preserve">
</head>
```

There are two options to send as `content`:

- `preserve`: Preserves the scroll position.
- `reset` (default): Resets the scroll position to the top of the page.

---

# Exclude from morphing

### Use case

You need to exclude HTML elements from being morphed when navigating. 

E.g an alert message.

```html
<div data-turbo-permanent>...</div>
```

---

# Turbo frames

Turbo Frames are a way to isolate parts of a page into independent contexts, which can be lazily loaded and updated.

```html
<turbo-frame id="my-refreshing-frame" refresh="morph">
  ...
</turbo-frame>
```

---
layout: fact
---

# Questions?
