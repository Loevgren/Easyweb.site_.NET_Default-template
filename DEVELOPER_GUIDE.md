# Easyweb.Site — Developer Guide

> A comprehensive guide for developing front-end sites with [Easyweb Headless CMS](https://easyweb.site).  
> This document covers the view system, tag helpers, key resolution, and everything needed to render CMS content.

---

## Table of Contents

1. [How It Works](#how-it-works)
2. [Easyweb CMS Concepts](#easyweb-cms-concepts)
3. [Project Structure](#project-structure)
4. [Views — Where They Live and How They Resolve](#views)
5. [The Key System](#the-key-system)
6. [Tag Helpers Reference](#tag-helpers-reference)
7. [Working with Lists](#working-with-lists)
8. [Layout System](#layout-system)
9. [Modules & Folders](#modules--folders)
10. [Menu & Navigation](#menu--navigation)
11. [Localization](#localization)
12. [Common Patterns & Examples](#common-patterns--examples)
13. [Tips & Tricks](#tips--tricks)

---

## How It Works

An Easyweb.Site project is a standard ASP.NET Core (.NET 8) web application. It does **not** contain any content or business logic — all content, templates, and structure are managed in the **Easyweb Studio** (the CMS backoffice). The web project's only job is to **render** that content using Razor views and Easyweb tag helpers.

The Easyweb.Site framework is installed as NuGet packages into your project. It provides tag helpers, routing, data access, and the rendering engine. The CMS (Studio) is where editors create pages and fill in content — your code simply presents it.

The data flow:

```
Easyweb Studio (CMS)  →  API / Data Source  →  Easyweb.Site Framework  →  Your Razor Views  →  HTML
         ↑                                              ↑                         ↑
   Editors create              Framework fetches       You write HTML +
   views, templates,           and builds the           ew- tag helpers
   articles & content          component tree           to render content
```

**The two halves:**

| Easyweb Studio (CMS) | Your Site Project |
|---|---|
| Define **views** (template trees) | Create `.cshtml` files that match view/template keys |
| Add **templates** to views (text, image, link, section) | Use `ew-for`, `ew-template`, `ew-list` to render template content |
| Create **articles** from views and fill in content | The framework resolves article data → your views display it |
| Organize into **modules** and **folders** | Mirror module structure in `Views/{moduleKey}/` folders |
| Configure template **settings** (custom key, content limits, required) | Reference templates by their **custom key** in tag helpers |

You write pure HTML/CSS with Easyweb tag helpers sprinkled in. No controllers, no models (in most cases), no database queries. The framework handles all data fetching.

---

## Easyweb CMS Concepts

Understanding how the CMS is structured is essential for knowing *what* your code renders and *why* keys are named the way they are.

### Views (in the CMS)

A **view** in the CMS is a blueprint for a type of page. It defines which templates (content fields) are available when an editor creates an article. A view is itself a template of type `ViewType`, making it the root of a template tree.

View types include:

| View Type | Purpose |
|---|---|
| `ViewType.Page` | Standard page (default) |
| `ViewType.Layout` | Layout wrapper (header, footer) |
| `ViewType.Settings` | Site-wide settings (logo, icons, colors) |
| `ViewType.Module` | Module root / folder view |
| `ViewType.Folder` | Folder-level view |

When you create a view called "Pages" with templates for `heroPage`, `flex`, etc., every article created from that view can fill in content for those templates.

### Templates

Templates are the **content containers** within a view. They form a **tree structure** — a template can have a parent and children. The editor sees this tree when filling in content for an article.

There are four primary template categories:

| Category | Type prefix | What the editor fills in | Example |
|---|---|---|---|
| **Text** | `DisplayType` | Text fields, HTML editor, numbers | `DisplayType.Text.header`, `DisplayType.HtmlEditor.html` |
| **Image** | `ReferenceType` | Image upload/selection | `ReferenceType.Image.hero-image` |
| **Link** | `ReferenceType` | URL, internal page link, document | `ReferenceType.Link.read-more` |
| **Section** | `SectionType` | Groups child templates (container) | `SectionType.Section.hero` |

A **section** is primarily a grouping container for child templates. For example, a "hero" section might contain a text template for the header, another for the description, an image template for the background, and a link template for the CTA button.

### The 3-Part Template Key

Every template in the CMS has a **full key** with three dot-separated parts:

```
[Type].[Key].[CustomKey]
```

| Part | Description | Example |
|---|---|---|
| **Type** | The main category | `DisplayType`, `ReferenceType`, `SectionType` |
| **Key** | The specific content type | `Text`, `HtmlEditor`, `Image`, `Link`, `Section` |
| **CustomKey** | Your chosen identifier (set by you) | `header`, `hero-image`, `newsList` |

Full key examples:
```
DisplayType.Text.header          → A text field with custom key "header"
ReferenceType.Image.hero-image   → An image with custom key "hero-image"
SectionType.Section.hero         → A section grouping with custom key "hero"
DisplayType.HtmlEditor.html      → A rich text editor with custom key "html"
ReferenceType.Link.read-more     → A link with custom key "read-more"
```

**In your code, you almost always use just the custom key** (the last part). The framework matches it automatically:

```razor
@* You write the custom key — the framework resolves the full key *@
<h1 ew-for="header"></h1>              @* matches DisplayType.Text.header *@
<img ew-for-src="hero-image" />         @* matches ReferenceType.Image.hero-image *@
<a ew-for-href="read-more">More</a>    @* matches ReferenceType.Link.read-more *@
```

### Template Settings

Each template in the CMS has configurable settings:

- **Custom Key** — The identifier you use in code (most important setting)
- **Content Limit** — How many content entries are allowed (e.g., an image template with limit 3 lets editors add up to 3 images; `null` = unlimited)
- **Required** — Whether content is mandatory for the template
- **Name / Description** — Display labels shown to editors in the Studio
- **Options** — JSON-based options for CSS classes, element IDs, and other rendering behavior

When content limit > 1 or is unlimited, the template becomes a **list** that you iterate with `ew-list` in your code.

### Articles

An **article** is a page of content. It is created *from* a view — the view determines which templates are available, and the editor fills in the content per template.

```
View "Pages"                    Article "About Us"
├── heroPage (section)     →    ├── heroPage
│   ├── header (text)      →    │   ├── header: "About us"
│   ├── description (text) →    │   ├── description: "At Brewly..."
│   └── media (image)      →    │   └── media: about-hero.jpg
└── flex (section, list)   →    └── flex
    ├── textSection        →        ├── textSection: { header: "Why us?", html: "<p>...</p>" }
    └── banner             →        └── banner: { tag: "...", header: "..." }
```

The left side is an example of the **view definition** (set up once in Studio). The right side is the **article content** (filled in by editors per page). Your `.cshtml` files render the right side using keys that match the left side.

### Modules and Folders

The site structure is organized into **modules** and **folders**:

- A **module** is a root-level container (like "Home", "Pages", "News"). It acts as a namespace and determines routing.
- A **folder** is a sub-container within a module that holds articles. The module root is itself a folder.
- Each module and folder can have its own article (the folder page / archive page).

```
Module: News (webModuleId: 4325)
└── Folder: News (the module root, isFolder: true)
    ├── Article: "Eco-Friendly Packaging..."   (isFolder: false)
    ├── Article: "Unveiling Brewly's First..."  (isFolder: false)
    └── Article: "Brewly Partners with..."      (isFolder: false)
```

In your code, you navigate the hierarchy with locator keys:
- `$Module` → the module root
- `$Folder` → the parent folder of the current article
- `$Article` → the current article

---

## Example Project Structure

```
httpdocs/
├── Views/
│   ├── _ViewImports.cshtml       ← Imports tag helpers and base page
│   ├── _ViewStart.cshtml         ← Sets layout (_Layout)
│   ├── Index.cshtml              ← Default/fallback view
│   │
│   ├── _Layout/                  ← Layout views
│   │   ├── _Layout.cshtml        ← Main HTML shell (DOCTYPE, head, body)
│   │   ├── Header.cshtml         ← Header/navbar
│   │   ├── _HeaderLogo.cshtml    ← Logo within header
│   │   ├── Menu.cshtml           ← Main navigation
│   │   └── Footer.cshtml         ← Footer
│   │
│   ├── Home/                     ← Home module views (Module key: "Home")
│   │   └── Home.cshtml           ← viewKey: "Home" or "" (default module page view)
│   │
│   ├── Pages/                    ← Pages module views (Module key: "Pages")
│   │   ├── Pages.cshtml          ← Default page view (viewKey: "Pages" or "" (default module page view))
│   │   └── Contact.cshtml        ← Alternative view (viewKey: "contact")
│   │
│   ├── News/                     ← News module views (Module key: "News")
│   │   ├── Module.cshtml         ← Archive/folder view (viewKey: "Module")
│   │   ├── News.cshtml           ← Individual article view (viewKey: "News" or "" (default module page view))
│   │   └── _newsList.cshtml      ← List item card/partial view
│   │
│   ├── Shared/                   ← Shared template views (any module)
│   │   ├── _banner.cshtml        ← Partial view/component view
│   │   ├── _textSection.cshtml   ← Partial view/component view
│   │   ├── _textMedia.cshtml     ← Partial view/component view
│   │   └── _hero.cshtml          ← Partial view/component view
│   │
│   └── _Default/                 ← Fallback views
│       ├── Template.cshtml       ← Default template rendering
│       └── Static.cshtml         ← Error pages, 404, etc.
│
├── wwwroot/                      ← Static assets (CSS, JS, images)
└── Resources/                    ← Localization JSON files
```

---

## Views

### How View Resolution Works

When a page is requested, the framework determines which `.cshtml` to render based on two things:

1. **Module key** — which CMS module the page belongs to (e.g., "Home", "Pages", "News")
2. **View key** — set per page/article in the Studio (e.g., "Home", "Pages", "Contact", "Module"). An empty view key defaults to the module key.

The framework searches these paths **in order** and uses the first match:

| Priority | Path | When |
|---|---|---|
| 1 | `/Views/{moduleKey}/{viewName}.cshtml` | Module-specific views |
| 2 | `/Views/_Layout/{viewName}.cshtml` | Layout components |
| 3 | `/Views/_Templates/{viewName}.cshtml` | Template-specific views |
| 4 | `/Views/_Templates/_Sections/{viewName}.cshtml` | Section templates |
| 5 | `/Views/_Templates/_Elements/{viewName}.cshtml` | Element templates |
| 6 | `/Views/Shared/{viewName}.cshtml` | Shared/global views |
| 7 | `/Views/_Default/{viewName}.cshtml` | Fallback views |

### Page Views vs. Component-/Template Views

There are two types of views:

**Page views** — The main view for a page/article. Determined by the page's `viewKey` in the CMS.

```
Page "About" (viewKey: "Pages", module: "Pages")
  → resolves to: Views/Pages/Pages.cshtml

Page "Contact" (viewKey: "Contact", module: "Pages")
  → resolves to: Views/Pages/Contact.cshtml
```

**Template views** — Views for individual CMS templates rendered within a page (hero, banner, teams, etc.). Determined by the template's `CustomKey` or `TypeKey`. Also referred to as component views.

```
Template with customKey "hero"
  → resolves to: Views/Shared/_hero.cshtml

Template with customKey "textMedia"
  → resolves to: Views/Shared/_textMedia.cshtml
```

Underscore prefix is optional.

### The `_ViewImports.cshtml`

This file is essential. It enables all Easyweb functionality in your views:

```razor
@inherits ViewTemplatePage<TModel>
@using Easyweb.Site.Infrastructure
@using Easyweb.Site.Core
@addTagHelper *, Easyweb.Site.TagHelpers
@addTagHelper *, Easyweb.Site.Infrastructure
```

`ViewTemplatePage<TModel>` is the base page class that provides `RequestContext`, `Translate()`, `Value()`, and other helpers.

### The `_ViewStart.cshtml`

Sets the layout and handles AJAX requests:

```razor
@{
    if (RequestContext.IsAjaxRequest && !ViewContext.HttpContext.Request.Query.ContainsKey("layout"))
        Layout = null;
    else
        Layout = "_Layout";
}
```

---

## The Key System

The key system is the heart of content rendering. Keys are strings that navigate the **template tree** (component tree) and extract values. They work like paths — similar to how CSS selectors or file paths work — letting you "walk" the tree of templates and content.

### Key Syntax

```
[locator]/[templateKey]/[valueKey]
```

- **`/`** separates key segments (like path separators)
- **`$`** prefix denotes special locator keys and value keys
- **No prefix** = template key (searches children by their **custom key**)
- You can chain multiple segments to traverse deep into the tree

Examples:
```
hero/header          → find child "hero", then its child "header", get value
$Article/$Label      → navigate to article, get label
$Folder/$Url         → navigate to folder, get URL
$settings/icon       → navigate to settings view, find "icon" child
section/preamble     → find child "section", then "preamble", get value
$Module/shared-link  → navigate to the module root, find template "shared-link"
$Parent/sidebar      → go to parent template, find "sidebar" child
```

**Key matching:** When you write `hero/header`, the framework searches children for a template whose custom key (or full key) matches `"hero"`, then within that template's children for `"header"`. You don't need to write the full 3-part key (`SectionType.Section.hero`) — just the custom key (`hero`) is enough.

### Locator Keys — Navigate the Tree

| Key | Resolves to |
|---|---|
| `$this` or `$` | Current renderable (default) |
| `$Article` | Closest article in the tree |
| `$Parent` | Parent renderable |
| `$Parents` | All ancestors (searched bottom-up) |
| `$Folder` | Folder of the current linkable |
| `$Module` | Module of the current linkable |
| `$Layout` | Layout view |
| `$Settings` or `$settings` | Settings view |
| `$Page` | Page-level view |
| `$View` | Closest view in the tree |
| `$Linkable` | Current linkable entity |
| `$Template` | Current WebTemplate |
| `$Content` | Current WebContent |

### Value Keys — Extract Data

| Key | Returns |
|---|---|
| `$Label` | Label/title of the current item |
| `$Value` | Primary value (often URL for links/media) |
| `$Url` | Generated URL for the item |
| `$HtmlValue` | Rich HTML content |
| `$Introduction` | Introduction/preamble text |
| `$Contents` | Full contents |
| `$Date` | Show date (formatted as `yyyy-MM-dd`) |
| `$Time` | Show time |
| `$Id` | Item ID |
| `$Index` | Position within parent's children |
| `$Count` | Number of items (for lists) |
| `$ChildCount` | Number of children |
| `$Color` | Color value |
| `$Show` | Show date as DateTime |
| `$OrderBy` | Sort order value |
| `$Caption` | Image description/caption |
| `$ViewKey` | View key of the article |
| `$IsFolder` | Whether item is a folder |
| `$Locked` | Whether item is locked |
| `$Created` | Creation date |
| `$Edited` | Last edited date |
| `$Empty` | Whether children list is empty |

### Context and Scoping

A fundamental concept: **every view and every `ew-list` iteration has a context** — a "current position" in the template tree. All key lookups are relative to this context.

When a template view renders, the **key context changes** to that template. So inside `hero.cshtml`, keys are relative to the hero template:

```razor
@* In Home.cshtml — context is the page (root of the article's template tree) *@
<h1 ew-for="hero/header"></h1>        @* navigate: page → hero → header *@

@* In hero.cshtml — context IS the hero (we're inside the hero section) *@
<h1 ew-for="header"></h1>             @* navigate: hero → header *@
```

The same scoping applies to `ew-list`. When iterating a list, the inner HTML is scoped to each individual item:

```razor
@* Context is the page *@
<div ew-list="newsList">
    @* Context is now each news article in the list *@
    <h3 ew-for="$Label"></h3>           @* This article's label *@
    <p ew-for="section/preamble"></p>    @* This article's section → preamble *@
</div>
```

You can always "escape" the current scope using locator keys:

```razor
@* Inside an article, but need data from the module root *@
<a ew-for-href="$Module/shared-link">Shared link from module</a>

@* Inside a list item, but need the parent list's header *@
<span ew-for="$Parent/header"></span>
```

---

## Tag Helpers Reference

### `ew-for` — Render Values

The primary tag helper for outputting CMS content. Resolves a key and renders the value as the element's inner content.

```razor
@* Text content *@
<h1 ew-for="header"></h1>
<p ew-for="description"></p>
<span ew-for="$Article/$Label"></span>

@* Image src *@
<img ew-for-src="media" />
<img ew-for-src="hero/media" class="img-fluid" />

@* Link href *@
<a ew-for-href="link">Click here</a>
<a ew-for-href="$Folder" ew-for="$Folder/$Label"></a>
<link ew-for-href="$settings/icon" rel="icon" />

@* Background image *@
<div ew-for-background-image="hero/media"></div>

@* Any HTML attribute *@
<div ew-for-attr-title="$Label"></div>
<div ew-for-attr-data-id="$Id"></div>
```

**Date formatting** with the `format` attribute:

```razor
<time ew-for="date" format="MMMM dd, yyyy"></time>     @* → "September 18, 2024" *@
<time ew-for="$Date" format="yyyy-MM-dd"></time>        @* → "2024-09-18" *@
<span ew-for="$Show" format="dd MMM"></span>            @* → "18 Sep" *@
```

**Advanced attributes:**

```razor
@* Cut text by word boundary *@
<p ew-for="description" cut-by-last-word="120"></p>

@* Strip HTML tags *@
<p ew-for="html" strip-html="true"></p>

@* Transform the value *@
<span ew-for="$Label" string-transform='x => x.ToUpper()'></span>

@* Format strings *@
<span ew-for="price" ensure-postfix=":-"></span>

@* Conditionally render *@
<span ew-for="subtitle" only-when-value='x => !string.IsNullOrEmpty(x)'></span>
```

### `<ew-template>` — Render Templates (Partial Views)

Renders child templates by delegating to their respective view files. **This is the mechanism that makes templates act like partial views** — when `ew-template` encounters a child template, it looks for a `.cshtml` file matching that template's key and renders it instead of the default rendering.

```razor
@* Render ALL child templates (each finds its own view) *@
<ew-template />

@* Render a specific child by key *@
<ew-template for-key="hero" />
<ew-template for-key="$settings/announcement" />

@* Render all except specific keys *@
<ew-template skip-keys="header, footer" />
```

When `<ew-template for-key="hero" />` executes:
1. Finds the child template with key "hero"
2. Determines its `ViewName` (from CustomKey or TypeKey)
3. Searches view resolution paths for a matching `.cshtml`
4. Renders that view with the hero template as context

If no matching `.cshtml` is found, it falls back to `_Default/Template.cshtml` which simply renders the template's children.

**Without `for-key`**, `<ew-template />` iterates over ALL children of the current template and renders each one using its own view. This is useful as a catch-all at the end of a page view.

### `ew-list` — Iterate Over Lists

**Essential for rendering arrays/lists.** When a template contains a list (like `newsList`, `teamMembers`, etc.), you **must** use `ew-list` to iterate over each item.

```razor
@* Loop over each item in newsList *@
<div class="row" ew-list="newsList">
    <div class="col">
        <h3 ew-for="$Label"></h3>
        <p ew-for="section/preamble"></p>
    </div>
</div>
```

The element with `ew-list` is **repeated** for each item in the list. The context inside changes to each individual item.

**Inside a template view** (when context is already the list component), use `$` or `$this`:

```razor
@* In newsList.cshtml — context is already newsList *@
<div class="col" ew-list="$">
    <div class="card">
        <h5 ew-for="$Label"></h5>
        <p ew-for="section/preamble"></p>
    </div>
</div>
```

**Filtering and sorting:**

```razor
@* Filter items *@
<div ew-list="teamMembers" only-when='x => x.Id != 0'>
    ...
</div>

@* Sort ascending *@
<div ew-list="newsList" order-by='x => x.WebContent?.OrderBy'>
    ...
</div>

@* Sort descending *@
<div ew-list="newsList" order-by-desc='x => x.WebContent?.Edited'>
    ...
</div>

@* Limit results *@
<ew-list for-key="newsList" take="3">
    ...
</ew-list>
```

**`ew-list` vs `ew-template`:**

| | `ew-list` | `ew-template` |
|---|---|---|
| **Use when** | You want to control the HTML for each item inline | You want each item to render using its own `.cshtml` view file |
| **Iteration** | Repeats the element for each item | Renders each child using view resolution |
| **Context** | Inner content gets each item as context | Each item's view gets the item as context |

### `ew-if-key` / `ew-if-not-key` — Conditional by Key

Show or hide elements based on whether a CMS key exists and has content.

```razor
@* Only show if "media" exists *@
<div ew-if-key="media">
    <img ew-for-src="media" />
</div>

@* Only show if "reversed" does NOT exist *@
<section ew-if-not-key="reversed">
    ...
</section>

@* Multiple keys with AND (all must exist) *@
<div ew-if-key="header && description">...</div>

@* Multiple keys with OR (any must exist) *@
<div ew-if-key="icon || logotype">...</div>
```

### `ew-if` / `ew-if-not` — Conditional by Expression

Show or hide based on C# expressions. Evaluates against null, false, 0, empty string, and empty collections.

```razor
@* C# expression *@
<div ew-if="@(Model.Nodes.Count > 0)">Has items</div>
<div ew-if="@Model.Nodes">Has nodes</div>
<div ew-if-not="@someVariable">Show when empty/null</div>
```

### `ew-scope` — Change Context

Wraps an area to set the key context to a specific template, without iterating.

```razor
@* Everything inside resolves relative to "hero" *@
<div ew-scope="hero">
    <h1 ew-for="header"></h1>
    <p ew-for="description"></p>
    <img ew-for-src="media" />
</div>
```

### `ew-translate` — Localization

```razor
@* Translate using key, with fallback text *@
<span ew-translate="Welcome_Title">Welcome</span>
<ew-translate for-key="Button_ReadMore">Read more</ew-translate>

@* In C# code *@
<span>@Translate("Welcome_Title", "Welcome")</span>
```

### `ew-href` — Link to Linkable Object

Used in C# loops with object references (not key strings):

```razor
@foreach (var menu in Model.Nodes)
{
    <a ew-href="@menu">@menu.Label</a>
}
```

### `ew-active-for` / `ew-active-class` — Active State

Adds a CSS class when the current page matches the linked item:

```razor
<li ew-active-class="active" ew-active-for="@menu">
    <a ew-href="@menu">@menu.Label</a>
</li>
```

### Layout Tag Helpers

```razor
<html ew-lang>                          @* Sets lang attribute from site culture *@
<body ew-body-classes>                  @* Adds CMS-driven CSS classes to body *@
<ew-layout-template pre-body="true" />  @* Renders layout templates before main content *@
<ew-layout-template post-body="true" /> @* Renders layout templates after main content *@
<ew-albasync />                         @* Live sync script *@
<ew-live-edit />                        @* Live edit overlay for Studio *@
```

---

## Working with Lists

Lists (ViewListComponent) are one of the most important concepts. They represent arrays of articles — like news listings, team members, related items, etc.

### Rendering a List with Inline HTML

Use `ew-list` when you want full control over each item's markup:

```razor
<div class="row row-cols-1 row-cols-md-3 g-4">
    <div class="col" ew-list="newsList">
        <div class="card h-100">
            <div class="card-body">
                <time ew-for="date" format="MMM dd, yyyy"></time>
                <h5><a ew-for-href="$" ew-for="$Label"></a></h5>
                <p ew-for="section/preamble"></p>
            </div>
        </div>
    </div>
</div>
```

### Rendering a List via Template Views

Use `<ew-template>` when each list item has its own view file:

```razor
<ew-template for-key="flex" />
```

Each item in `flex` renders using its own view (determined by its CustomKey). Different flex items can use different views (e.g., `textSection.cshtml`, `banner.cshtml`, `teams.cshtml`).

### List Item View Files

When a list is rendered with `<ew-template>`, each item's view name comes from the parent list's **CustomKey**. For example, a `ViewListComponent` with CustomKey `"newsList"` will look for:

```
Views/{moduleKey}/newsList.cshtml → Views/Shared/newsList.cshtml → _Default/Template.cshtml
```

Inside that view, use `ew-list="$"` to iterate since the context is the list component:

```razor
@* Views/News/newsList.cshtml *@
<div class="col" ew-list="$">
    <div class="card h-100 border-0 shadow-sm">
        <div class="card-body">
            <time ew-for="date" format="MMM dd, yyyy"></time>
            <h5 class="card-title fw-bold">
                <a ew-for-href="$" ew-for="$label" class="stretched-link text-decoration-none text-body"></a>
            </h5>
            <p class="card-text text-body-secondary" ew-for="section/preamble"></p>
        </div>
    </div>
</div>
```

---

## Layout System

The layout (`_Layout.cshtml`) is the HTML shell that wraps all pages:

```razor
<!DOCTYPE html>
<html ew-lang>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="~/css/css.min.css" asp-append-version="true" />
    <link ew-for-href="$settings/icon" rel="shortcut icon" ew-if-key="$settings/icon" />
</head>

<body ew-body-classes>
    <ew-template for-key="$settings/announcement" />
    <ew-layout-template pre-body="true" />

    <main>
        @RenderBody()
    </main>

    <ew-layout-template post-body="true" />
    <ew-albasync />
    <ew-live-edit />
</body>
</html>
```

**Key layout concepts:**

- `$settings` — the site settings view (logo, icons, announcement, etc.)
- `<ew-layout-template pre-body="true" />` — renders layout templates marked as "before body" in Studio (typically Header)
- `<ew-layout-template post-body="true" />` — renders layout templates marked as "after body" in Studio (typically Footer)
- `@RenderBody()` — where the page view's content goes
- Layout views live in `Views/_Layout/` and are resolved by their template key (e.g., `Header`, `Footer`, `Menu`, `HeaderLogo`)

---

## Modules & Folders

Each section of a site is a **module** in the CMS (Home, Pages, News, etc.). Modules are the top-level organizational unit — they define both the URL structure and the view folder.

In the Studio, you create modules and assign them a key (e.g., "News"). Folders within a module organize articles into groups (e.g., news categories). The module root is itself a folder that can have its own article (the archive/landing page).

Modules have:

- A **module key** — determines the view folder (`Views/{moduleKey}/`) and the URL prefix
- An **archive/folder view** — the page shown when visiting the module root (typically `viewKey: "Module"`)
- One or more **article views** — different page layouts for articles within the module
- **Folders** — sub-sections that group articles (each folder can also have its own folder page)

### From Studio to Code

The connection between what you set up in the Studio and what you write in code:

```
Studio                              Your Code
──────────────────────────────────    ────────────────────────────────────────────────

Module "News" (key: News)       →   Views/News/ folder
View "Module" (archive page)    →   Views/News/Module.cshtml
View "News" (article page)      →   Views/News/News.cshtml
Template "newsList" (list)      →   <ew-template for-key="newsList" />
                                    → Views/News/newsList.cshtml (item view)
Template "heroPage" (section)   →   ew-for="heroPage/header"
Template "section" (section)    →   ew-for="section/preamble"
Template "date" (text)          →   ew-for="date"
Content limit = 1 (single item) →   ew-for (just output the value)
Content limit > 1 or null       →   ew-list (iterate over items)
```

The **content limit** set on a template in the Studio determines whether you use `ew-for` (single value) or `ew-list` (list of values) in your code. If an image template has content limit 3, editors can add up to 3 images, and you iterate them with `ew-list`.

### Module View Structure

```
Views/News/
  Module.cshtml     ← Folder/archive view (listing page)
  News.cshtml       ← Default article view
  newsList.cshtml   ← List item card view (for ViewListComponent)
```

### Archive Page Pattern

```razor
@* Views/News/Module.cshtml *@

@* Hero *@
<section ew-if-key="heroPage">
    <h1 ew-for="heroPage/header"></h1>
    <p ew-for="heroPage/description"></p>
</section>

@* Article listing *@
<section>
    <div class="row row-cols-1 row-cols-md-3 g-4">
        <ew-template for-key="newsList" />
    </div>
</section>
```

### Article Page Pattern

```razor
@* Views/News/News.cshtml *@

@* Breadcrumb *@
<nav>
    <ol class="breadcrumb">
        <li class="breadcrumb-item">
            <a ew-for-href="$Folder" ew-for="$Folder/$Label"></a>
        </li>
        <li class="breadcrumb-item active" ew-for="$Label"></li>
    </ol>
</nav>

@* Article content *@
<time ew-for="date" format="MMMM dd, yyyy"></time>
<h1 ew-for="$Label"></h1>
<p class="lead" ew-for="section/preamble"></p>
<div ew-for="section/html"></div>

@* Related articles *@
<section ew-if-key="newsList">
    <h2>More News</h2>
    <ew-template for-key="newsList" />
</section>
```

---

## Menu & Navigation

The menu is a special layout component with a typed model (`MenuFeature`). It provides `Model.Nodes` — a tree of menu items.

```razor
@model MenuFeature
<ul ew-if="@Model.Nodes">
    @foreach (var menu in Model.Nodes)
    {
        if (menu.Children.Count > 0)
        {
            <li ew-active-class="active" ew-active-for="@menu">
                <a href="#">@menu.Label</a>
                <ul>
                    @foreach (var child in menu.Children)
                    {
                        <li>
                            <a ew-href="@child">@child.Label</a>
                        </li>
                    }
                </ul>
            </li>
        }
        else
        {
            <li ew-active-class="active" ew-active-for="@menu">
                <a ew-href="@menu">@menu.Label</a>
            </li>
        }
    }
</ul>
```

- `ew-href="@menu"` — generates the URL for the menu item
- `ew-active-for="@menu"` + `ew-active-class="active"` — adds "active" class when the current page matches

---

## Localization

Translation files are JSON-based and stored in `/Resources/`. Access translations via:

```razor
@* Tag helper *@
<span ew-translate="ReadMore">Read more</span>

@* In Razor code *@
<span>@Translate("ReadMore", "Read more")</span>

@* With arguments *@
<span>@Translate("Welcome_User", "Welcome {0}!", user.Name)</span>
```

The first argument is the translation key, the second is the fallback text shown if no translation exists.

---

## Common Patterns & Examples

### Full-Width Hero with Background Image

```razor
<section class="position-relative text-white overflow-hidden" style="min-height: 85vh" ew-if-key="hero">
    <img ew-for-src="hero/media" class="position-absolute top-0 start-0 w-100 h-100 object-fit-cover" />
    <div class="position-absolute top-0 start-0 w-100 h-100 bg-dark bg-opacity-50"></div>
    <div class="container position-relative" style="min-height: 85vh; z-index: 2">
        <h1 ew-for="hero/header"></h1>
        <p ew-for="hero/description"></p>
        <a ew-for-href="hero/link" ew-if-key="hero/link">Learn More</a>
    </div>
</section>
```

### Side-by-Side Hero (Text + Image)

```razor
<section class="py-5" ew-if-key="heroPage">
    <div class="container">
        <div class="row align-items-center g-5">
            <div class="col-lg-6">
                <h1 ew-for="heroPage/header"></h1>
                <p ew-for="heroPage/description"></p>
            </div>
            <div class="col-lg-6" ew-if-key="heroPage/media">
                <img ew-for-src="heroPage/media" class="img-fluid rounded" />
            </div>
        </div>
    </div>
</section>
```

### Text Section (Rich HTML)

```razor
<section class="py-5">
    <div class="container" style="max-width: 800px">
        <h2 ew-for="header"></h2>
        <div ew-for="html"></div>     @* Preserves HTML tags from CMS editor *@
    </div>
</section>
```

### Alternating Layout with Reversed Flag

```razor
@* Normal: text left, image right *@
<section ew-if-not-key="reversed">
    <div class="row">
        <div class="col-lg-6">
            <h2 ew-for="header"></h2>
            <p ew-for="description"></p>
        </div>
        <div class="col-lg-6">
            <img ew-for-src="image" class="img-fluid" />
        </div>
    </div>
</section>

@* Reversed: image left, text right *@
<section ew-if-key="reversed">
    <div class="row">
        <div class="col-lg-6">
            <img ew-for-src="image" class="img-fluid" />
        </div>
        <div class="col-lg-6">
            <h2 ew-for="header"></h2>
            <p ew-for="description"></p>
        </div>
    </div>
</section>
```

### Flexible Content Blocks

When a page has a `flex` array of mixed content types:

```razor
@* Page view — delegates each flex item to its own view *@
<ew-template for-key="flex" />
```

Each flex item is a wrapper; its child determines which view renders:
- Child `textSection` → `Views/Shared/textSection.cshtml`
- Child `banner` → `Views/Shared/banner.cshtml`
- Child `teams` → `Views/Shared/teams.cshtml`

### Card Grid with List Items

```razor
<div class="row row-cols-1 row-cols-md-3 g-4">
    <div class="col" ew-list="newsList">
        <div class="card h-100">
            <div class="card-body">
                <time ew-for="date" format="MMM dd, yyyy"></time>
                <h5>
                    <a ew-for-href="$" ew-for="$label" class="stretched-link"></a>
                </h5>
                <p ew-for="section/preamble"></p>
            </div>
        </div>
    </div>
</div>
```

---

## Tips & Tricks

### 1. Always Guard with `ew-if-key`

Wrap sections with `ew-if-key` to prevent empty HTML when content is missing:

```razor
@* BAD — renders empty <section> if hero doesn't exist *@
<section>
    <h1 ew-for="hero/header"></h1>
</section>

@* GOOD — section only renders if hero has content *@
<section ew-if-key="hero">
    <h1 ew-for="hero/header"></h1>
</section>
```

### 2. Combine `ew-for-href` and `ew-for` on `<a>` Tags

Both attributes work together on the same element:

```razor
<a ew-for-href="$Folder" ew-for="$Folder/$Label" class="btn btn-primary"></a>
@* Produces: <a href="/news" class="btn btn-primary">News</a> *@
```

### 3. Use `ew-for` for HTML Content

`ew-for` preserves HTML from the CMS rich text editor:

```razor
<div ew-for="html"></div>
@* Output: <div><p>Rich text with <strong>formatting</strong></p></div> *@
```

### 4. Link Back to Module/Folder

```razor
<a ew-for-href="$Folder" ew-for="$Folder/$Label"></a>    @* → parent folder *@
<a ew-for-href="$Module">Back to module</a>                @* → module root *@
```

### 5. Access Settings/Global Content

Use `$settings` to reach site-wide settings configured in the Studio:

```razor
<img ew-for-src="$settings/logotype" />
<link ew-for-href="$settings/icon" rel="icon" ew-if-key="$settings/icon" />
```

### 6. `ew-list` vs `ew-template` for Lists

- Use **`ew-list`** when you want to write the card/item markup **inline**.
- Use **`<ew-template>`** when each item should use its own **view file** (good for reuse across pages).
- If using `<ew-template>`, the items still need `ew-list="$"` **inside their view** to iterate.

### 7. `ew-scope` for Cleaner Nested Keys

Instead of repeating the parent key:

```razor
@* Verbose *@
<h2 ew-for="introduction/header"></h2>
<p ew-for="introduction/description"></p>
<span ew-for="introduction/tag"></span>

@* Cleaner with ew-scope *@
<div ew-scope="introduction">
    <h2 ew-for="header"></h2>
    <p ew-for="description"></p>
    <span ew-for="tag"></span>
</div>
```

### 8. Image Tag Helpers

`ew-for-src` on `<img>` automatically handles alt text from the CMS image metadata:

```razor
<img ew-for-src="media" class="img-fluid" />
@* Produces: <img src="/upl/images/123.jpg" alt="Photo description" class="img-fluid" /> *@
```

### 9. Debugging — Check What Keys Exist

In Development, use the live edit overlay (`<ew-live-edit />`) in the layout. It shows the template tree and available keys directly in the browser.

### 10. The `Value()` Helper in Razor Code

Access template values in C# code blocks:

```razor
<div style="background-color: @Value("$settings/brandColor")">
    ...
</div>
```

### 11. How to Figure Out Available Keys

When starting with a new view or page type:

1. **Check the API response** — the JSON data from the CMS shows all available keys and their structure
2. **Use the Studio** — open the view in Easyweb Studio to see the template tree and all custom keys
3. **Use `<ew-live-edit />`** — in development, this overlay shows the live template tree in the browser
4. **JSON keys = template custom keys** — the property names in the API JSON response directly correspond to the custom keys you use in `ew-for`, `ew-template`, and `ew-list`

For example, if the API returns `{ "hero": { "header": "...", "media": {...} } }`, you know:
- `hero` is a section template with custom key "hero"
- `header` is a child text template with custom key "header"
- `media` is a child image template with custom key "media"
- Use `ew-for="hero/header"` to render the header text
- Use `ew-for-src="hero/media"` to render the image

---

## Quick Reference Card

```razor
@* ─── Output values ─── *@
<h1 ew-for="key"></h1>                           @* Text content *@
<img ew-for-src="key" />                          @* Image source *@
<a ew-for-href="key">Link</a>                     @* Link URL *@
<div ew-for-background-image="key"></div>          @* Background image *@
<time ew-for="key" format="yyyy-MM-dd"></time>     @* Formatted date *@

@* ─── Render templates ─── *@
<ew-template />                                    @* All children *@
<ew-template for-key="hero" />                     @* Specific child *@
<ew-template skip-keys="hero, banner" />           @* All except these *@

@* ─── Iterate lists ─── *@
<div ew-list="key">per-item markup</div>           @* Loop items inline *@
<div ew-list="$">per-item markup</div>             @* Loop current list *@

@* ─── Conditionals ─── *@
<div ew-if-key="key">...</div>                     @* Show if key has content *@
<div ew-if-not-key="key">...</div>                 @* Show if key is empty *@
<div ew-if="@expression">...</div>                 @* Show if C# truthy *@

@* ─── Context ─── *@
<div ew-scope="key">nested keys</div>              @* Change context *@

@* ─── Navigation ─── *@
<a ew-href="@menuItem">text</a>                    @* Link from C# object *@
<li ew-active-class="active"                       @* Active page detection *@
    ew-active-for="@menuItem">

@* ─── Translation ─── *@
<span ew-translate="Key">Fallback</span>           @* Localized text *@

@* ─── Layout ─── *@
<html ew-lang>                                     @* Language attribute *@
<body ew-body-classes>                              @* CMS body classes *@
<ew-layout-template pre-body="true" />             @* Layout before content *@
<ew-layout-template post-body="true" />            @* Layout after content *@
```
