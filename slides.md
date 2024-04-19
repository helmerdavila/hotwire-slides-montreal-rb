---
theme: "apple-basic"
fonts:
  sans: "Mulish"
layout: intro-image-right
image: ./images/hotwire.gif
monaco: true
---

# Hotwire - Ruby on Rails
### Intro and usage

---
layout: intro-image
image: ./images/wall.jpg
---

# About me

## Helmer Davila

### Profile

- Software Engineer with 9+ years of experience
- Spanish, English and French
- **Backend Developer:** PHP, Ruby, Python, Node.js, a lot of AWS
- **Frontend Developer:** React, Angular 2+, Vue, React Native

### Fun facts

- First computer at 9 years old, Pentium II
- Former technical cofounder of a startup

---

# Goals of this presentation

- Learn how **Hotwire Turbo (Drive, Frames, Streams)** works
- **Avoid using Javascript**, until you really need it

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
layout: intro-image
image: ./images/wall.jpg
---

<div class="absolute left-10 bottom-10">
  <h1>Turbo Drive</h1>
  <p>Turbo Drive replaces HTML elements in the current page with the corresponding elements from the response, without a full page reload</p>
</div>

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

# Turbo Drive: Reload assets when changed

Add `data-turbo-track="reload"` to the `<link>` and `<script>` tags to reload them when the page changes.

Make sure that you have a unique identifier in the file name.

If you are using the gem Turbo Rails, it will automatically add the unique identifier to the file name.

```erb
<%= stylesheet_link_tag "tailwind", "inter-font", "data-turbo-track": "reload" %>
<%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
```

```html
<head>
  ...
  <link rel="stylesheet" href="/application-258e88d.css" data-turbo-track="reload">
  <script src="/application-cbd3cd4.js" data-turbo-track="reload"></script>
</head>
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

# Example of Turbo Drive - Form Code

```erb
<%= form_with model: @job, url: local_assigns[:form_url] do |form| %>
  <div class="mb-2">
    <%= form.label :name, "Role", class: "block font-semibold" %>
    <%= form.text_field :name, class: "w-full rounded" %>
  </div>
  <div class="mb-2">
    <%= form.label :status, class: "block font-semibold" %>
    <%= form.text_field :status, class: "w-full rounded" %>
  </div>
  <div class="mb-2">
    <%= form.label :tag, class: "block font-semibold" %>
    <%= form.collection_select :tag_id, @tags, :id, :name, {}, { class: "w-full rounded" } %>
  </div>
  <% if local_assigns[:update] %>
    <%= form.submit "Update job", class: "mt-7 bg-black text-white rounded w-full py-3 font-semibold" %>
  <% else %>
    <%= form.submit "Create job", class: "mt-7 bg-black text-white rounded w-full py-3 font-semibold" %>
  <% end %>
<% end %>
```

---

# Example of Turbo Drive - Updating configuration

```js
// Configure your import map in config/importmap.rb. Read more: https://github.com/rails/importmap-rails
import { Turbo } from "@hotwired/turbo-rails"

// Reduce delay for progress bar from 500ms to 0ms
Turbo.setProgressBarDelay(0);

// This one doesn't keep history
const navigateToJobsLink = document.querySelector('#navigate-to-jobs');
if (navigateToJobsLink) {
    navigateToJobsLink.addEventListener('click', () => Turbo.visit('/jobs'));
}
```

---

# Turbo Drive gem - Module: Turbo::DriveHelper

```erb
<!-- app/views/application.html.erb -->
<!-- Required to work -->
<html><head><%= yield :head %></head><body><%= yield %></html>

<!-- app/views/trays/index.html.erb -->
<% turbo_exempts_page_from_cache %>
<p>Page that shouldn't be cached by Turbo</p>
```

```erb
<% turbo_exempts_page_from_cache %>
<% turbo_exempts_page_from_cache_tag %>
<% turbo_page_requires_reload %>
<% turbo_page_requires_reload_tag %>
<% turbo_refresh_method_tag(method = :replace) %>
<% turbo_refresh_scroll_tag(scroll = :reset) %>
<% turbo_refreshes_with(method: :replace, scroll: :reset) %>
```

---

# Testing in Turbo Drive

Testing is the same as testing a regular Rails application

```ruby
class JobsControllerTest < ActionDispatch::IntegrationTest
  test "should get index" do
    get jobs_path
    assert_response :success
  end

  test "should create job" do
    job_count_first = Job.count
    post jobs_path, params: { job: { name: 'New Job', status: :active, tag_id: tags(:tag_ruby).id } }
    job_count_last = Job.count

    assert_equal job_count_first + 1, job_count_last
    assert_redirected_to job_path(Job.last)
  end

  # ... other tests
end
```

---
layout: fact
---

# Demo

## Turbo Drive


---
layout: intro-image
image: ./images/wall.jpg
---

<div class="absolute bottom-10 left-10">
  <h1>Turbo Frames</h1>
  <p>Isolate parts of a page into independent contexts, which can be lazily loaded and updated</p>
</div>

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

# Turbo frames `<turbo-frame>` HTML attributes

### HTML attributes

- `src` URL or path value that controls navigation of the element.
- `loading` with two values `eager` and `lazy`. `loading="eager"` will immediately load the frame, while `loading="lazy"` will load the frame when it becomes visible.
- `busy` boolean attribute that indicates if the frame is currently loading. Managed by Turbo.
- `disabled` for disabling frame navigation.
- `complete` boolean attribute that indicates if the frame has finished loading. Managed by Turbo.
- `autoscroll` boolean attribute that indicates if the frame should scroll to the top after loading. Managed by Turbo.

---

# Turbo frames properties

The same properties can be controlled from JavaScript.

- `FrameElement.src`
- `FrameElement.disabled`
- `FrameElement.loading`
- `FrameElement.loaded`
- `FrameElement.complete`
- `FrameElement.autoscroll`
- `FrameElement.isActive`
- `FrameElement.isPreview`

# Turbo frames functions

- `FrameElement.reload()`

---

# T. Frame with gem - Module: Turbo::FramesHelper

```erb
<%= turbo_frame_tag "tray", src: tray_path(tray) %>
# => <turbo-frame id="tray" src="http://example.com/trays/1"></turbo-frame>

<%= turbo_frame_tag tray, src: tray_path(tray) %>
# => <turbo-frame id="tray_1" src="http://example.com/trays/1"></turbo-frame>

<%= turbo_frame_tag "tray", src: tray_path(tray), target: "_top" %>
# => <turbo-frame id="tray" target="_top" src="http://example.com/trays/1"></turbo-frame>

<%= turbo_frame_tag "tray", target: "other_tray" %>
# => <turbo-frame id="tray" target="other_tray"></turbo-frame>

<%= turbo_frame_tag "tray", src: tray_path(tray), loading: "lazy" %>
# => <turbo-frame id="tray" src="http://example.com/trays/1" loading="lazy"></turbo-frame>

<%= turbo_frame_tag "tray" do %>
  <div>My tray frame!</div>
<% end %>
# => <turbo-frame id="tray"><div>My tray frame!</div></turbo-frame>

<%= turbo_frame_tag [user_id, "tray"], src: tray_path(tray) %>
# => <turbo-frame id="1_tray" src="http://example.com/trays/1"></turbo-frame>
```

---

# Turbo Frames - Example using gem functions

````md magic-move

```erb
<section class="border-sky-700 border-8 bg-white p-20 shadow-2xl w-full">
  <h1 class="text-3xl font-bold text-black mb-5">🔨 Jobs</h1>
  <%= turbo_frame_tag JobFrameController::JOB_FRAME_ID do %>
    <%= link_to 'Create new job', new_job_frame_path, class: "block py-6 underline" %>
    <table class="border border-collapse border-slate-400 w-96">
      <thead>
      ...
      <tbody>
      <% @jobs.each do |j| %>
        <tr>
          <td class="border border-slate-400 text-left px-2 py-3"><%= j.name %></td>
          <td class="border border-slate-400 text-left px-2 py-3"><%= j.status %></td>
          <td class="border border-slate-400 text-left px-2 py-3"><%= j.tag.name %></td>
          <td class="border border-slate-400 text-left px-2 py-3">
            <%= link_to 'Show', job_frame_path(j), class: "underline" %>
          </td>
        </tr>
      <% end %>
      </tbody>
    </table>
    <%= render "dashboard/root" %>
  <% end %>
</section>
```

```erb
<%= turbo_frame_tag JobFrameController::JOB_FRAME_ID do %>
<% end %>
```

```ruby
class JobFrameController < ApplicationController
  JOB_FRAME_ID = "jobs-frame"

  def index
    @jobs = Job.includes(:tag).all
  end

  # ... other actions
end
```

````

---

# Turbo Frames - Example using HTML

````md magic-move

```erb
<section class="border-sky-700 border-8 bg-white p-20 shadow-2xl w-full flex flex-row">
  <section class="mr-7">
    <h1 class="text-5xl font-bold mb-5">New tag</h1>
    <turbo-frame id="<%= TagFrameController::TAG_FRAME_FORM_ID %>" src="<%= new_tag_frame_path(@tag) %>">
    </turbo-frame>
  </section>
  <section>
    <h1 class="text-5xl font-bold mb-5">🏷️ Tags</h1>
    <turbo-frame id="<%= TagFrameController::TAG_FRAME_ID %>">
      <table class="border border-collapse border-slate-400 w-96">
        <thead>
        <tr>
          <th class="border border-slate-400 text-left px-2 py-3">Name</th>
        </tr>
        </thead>
        <tbody>
        <% @tags.each do |tag| %>
          <tr>
            <td class="border border-slate-400 text-left px-2 py-3"><%= tag.name %></td>
          </tr>
        <% end %>
        </tbody>
      </table>
    </turbo-frame>
  </section>
</section>
```

```erb
<!-- app/views/tag_frame/index.html.erb -->
<turbo-frame id="<%= TagFrameController::TAG_FRAME_FORM_ID %>" src="<%= new_tag_frame_path(@tag) %>">
</turbo-frame>
<turbo-frame id="<%= TagFrameController::TAG_FRAME_ID %>">
  <!-- code to display the table -->
</turbo-frame>
```

```erb
<!-- app/views/tag_frame/new.html.erb -->
<turbo-frame id="<%= TagFrameController::TAG_FRAME_FORM_ID %>">
  <%= form_with model: @tag, url: tag_frame_index_path, data: { turbo_frame: TagFrameController::TAG_FRAME_ID } do |form| %>
    <div class="mb-2">
      <%= form.label :name %>
      <%= form.text_field :name %>
    </div>
    <%= form.submit "Create tag", class: "block mt-7 py-3 bg-black rounded text-white text-center w-full" %>
  <% end %>
</turbo-frame>
```

```erb
<!-- app/views/tag_frame/new.html.erb -->
<turbo-frame id="<%= TagFrameController::TAG_FRAME_FORM_ID %>">
  <%= form_with ..., data: { turbo_frame: TagFrameController::TAG_FRAME_ID } do |form| %>
  <% end %>
</turbo-frame>
```

```ruby
class TagFrameController < ApplicationController
  TAG_FRAME_ID = "tags-frame"
  TAG_FRAME_FORM_ID = "tags-frame-form"

  def index
    @tags = Tag.all
    @tag = Tag.new
  end

  # ... other actions
end
```

````

---

# Turbo Frames - Testing

Tests also work the same as a regular Rails application.

```ruby
class TagFrameControllerTest < ActionDispatch::IntegrationTest
  test "should get index" do
    get tag_frame_index_path
    assert_response :success
  end

  test "should create tag" do
    post tag_frame_index_path, params: { tag: { name: 'New Tag' } }
    assert_redirected_to tag_frame_index_path
  end

  # ... other tests
end
```

---
layout: fact
---

# Demo

## Turbo Frames


---
layout: intro-image
image: ./images/wall.jpg
---

<div class="absolute bottom-10 left-10">
  <h1>Turbo Streams</h1>
  <p>Specify the behaviour of added elements to the UI</p>
</div>


---

# But, be careful...

_**It’s good practice to start your interaction design without Turbo Streams**_. Make the entire application work as it would if Turbo Streams were not available, then layer them on as a level-up. This means you won’t come to rely on the updates for flows that need to work in native applications or elsewhere without them.

---

# But What About Running JavaScript?

Turbo Streams consciously restricts you to eight actions: append, prepend, (insert) before, (insert) after, replace, update, remove, and refresh. If you want to trigger additional behavior when these actions are carried out, you should attach behavior using Stimulus controllers.

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

# Turbo Streams - Testing

Tests change a bit when using Turbo Streams. Because we need to specify the type of the response we are expecting

```ruby
class JobApplicationControllerTest < ActionDispatch::IntegrationTest
  test "should create job application" do
    job_application_count = Application.count
    post job_application_index_path, params: { application: { job_id: jobs(:job_javascript_sr_developer).id, name: 'John Doe', cover_letter: 'I am an excellent developer' } }
    job_application_count_after = Application.count
    assert_equal job_application_count + 1, job_application_count_after
  end

  test "should create job application with turbo stream" do
    job_application_count = Application.count
    post job_application_index_path, params: { application: { job_id: jobs(:job_javascript_sr_developer).id, name: 'John Doe', cover_letter: 'I am an excellent developer' } }, as: :turbo_stream
    job_application_count_after = Application.count
    assert_equal job_application_count + 1, job_application_count_after
    assert_response :success
  end

  # ... other tests
end
```


---
layout: fact
---

# Demo

## Turbo Streams

---
layout: intro-image
image: ./images/wall.jpg
---

<div class="absolute bottom-10 left-10">
  <h1>Testing</h1>
</div>

---
layout: fact
---

# Questions?
