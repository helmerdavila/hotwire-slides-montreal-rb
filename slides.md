---
theme: apple-basic
fonts:
  sans: "Quicksand"
layout: intro-image-right
image: ./images/hotwire.gif
monaco: true
---

# Hotwire - Ruby on Rails
### Intro and usage

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
layout: fact
---

# Turbo Drive

Turbo Drive replaces HTML elements in the current page with the corresponding elements from the response, without a full page reload.

---

# Example of Turbo Drive behaviour

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

### As javascript

```js
// Moving forward
Turbo.visit(location)
Turbo.visit(location, { action: "navigate" })
// Replacing top of history
Turbo.visit(location, { action: "replace" })
// Restore (Reserved for internal use, DON'T USE)
Turbo.visit(location, { action: "restore" })
```

### As Ruby (HTML)

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
layout: fact
---

# Turbo frames

Isolate parts of a page into independent contexts, which can be lazily loaded and updated.

---

# Example of a Turbo Frame

```html
<turbo-frame id="my-refreshing-frame" refresh="morph">
  ...
</turbo-frame>
```

---

# Basic frames

```html
<turbo-frame id="messages">
  <a href="/messages/expanded">
    Show all expanded messages in this frame.
  </a>

  <form action="/messages">
    Show response from this form within this frame.
  </form>
</turbo-frame>
```

---

# Eager loaded frame

```html
<turbo-frame id="messages" src="/messages">
  Content will be replaced when /messages has been loaded.
</turbo-frame>
```

---

# Lazy loaded frame

```html
<turbo-frame id="messages" src="/messages" loading="lazy">
  Content will be replaced when the frame becomes visible and /messages has been loaded.
</turbo-frame>
```

---

# Turbo frame `<turbo-frame>` HTML attributes

### HTML attributes

- `src` URL or path value that controls navigation of the element.
- `loading` with two values `eager` and `lazy`. `loading="eager"` will immediately load the frame, while `loading="lazy"` will load the frame when it becomes visible.
- `busy` boolean attribute that indicates if the frame is currently loading. Managed by Turbo.
- `disabled` for disabling frame navigation.
- `complete` boolean attribute that indicates if the frame has finished loading. Managed by Turbo.
- `autoscroll` boolean attribute that indicates if the frame should scroll to the top after loading. Managed by Turbo.

---

# Turbo frame properties

The same properties can be controlled from JavaScript.

- `FrameElement.src`
- `FrameElement.disabled`
- `FrameElement.loading`
- `FrameElement.loaded`
- `FrameElement.complete`
- `FrameElement.autoscroll`
- `FrameElement.isActive`
- `FrameElement.isPreview`

# Turbo frame functions

- `FrameElement.reload()`

---
layout: fact
---

# Turbo Streams

Specify the behaviour of added elements to the UI

---

## Append

Appends the content within the template tag to the container designated by the target dom id.

```html
<turbo-stream action="append" target="dom_id">
  <template>
    Content to append to container designated with the dom_id.
  </template>
</turbo-stream>
```

---

## Prepend

Prepends the content within the template tag to the container designated by the target dom id.

```html
<turbo-stream action="prepend" target="dom_id">
  <template>
    Content to prepend to container designated with the dom_id.
  </template>
</turbo-stream>
```

---

## Replace

Replaces the element designated by the target dom id.

```html
<turbo-stream action="replace" target="dom_id">
  <template>
    Content to replace the element designated with the dom_id.
  </template>
</turbo-stream>
```

---

## Update

Updates the content within the template tag to the container designated by the target dom id.

```html
<turbo-stream action="update" target="dom_id">
  <template>
    Content to update to container designated with the dom_id.
  </template>
</turbo-stream>
```

---

## Remove

Removes the element designated by the target dom id.

```html
<turbo-stream action="remove" target="dom_id">
</turbo-stream>
```

---

## Before

Inserts the content within the template tag before the element designated by the target dom id.

```html
<turbo-stream action="before" target="dom_id">
  <template>
    Content to place before the element designated with the dom_id.
  </template>
</turbo-stream>
```

---

## After

Inserts the content within the template tag after the element designated by the target dom id.

```html
<turbo-stream action="after" target="dom_id">
  <template>
    Content to place after the element designated with the dom_id.
  </template>
</turbo-stream>
```

---

## Refresh

Initiates a Page Refresh to render new content with morphing.

```html
<!-- without `[request-id]` -->
<turbo-stream action="refresh"></turbo-stream>

<!-- debounced with `[request-id]` -->
<turbo-stream action="refresh" request-id="abcd-1234"></turbo-stream>
```

---
layout: fact
---

# Questions?
