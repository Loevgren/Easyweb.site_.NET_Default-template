# Copilot Instructions — Albatross / Easyweb.Site

## Architecture Overview

**Easyweb.Site** is a .NET 8 CMS rendering engine for sites managed by [Easyweb Headless CMS](https://easyweb.site), shipped as NuGet packages consumed by downstream site projects. Company: **Sphinxly AB**.

## Developing web sites

Instructions for building front end/web sites with TagHelpers/HTML/Razor/.cshtml are located in file 'DEVELOPER_GUIDE.md' in the root of the repository.

### Project Dependency Graph

```
Easyweb.Site.Core           → Domain entities, interfaces, options, data contracts (no dependencies)
Easyweb.Site.DataApi        → REST API data source (IDataSource via LINQ-to-API with IdentityModel OAuth2)
Easyweb.Site.Infrastructure → MVC rendering engine: components, tag helpers, middleware, routing, startup
Easyweb.Site.RuntimeCompilation    → Razor runtime compilation support
Easyweb.Site.ThumbnailGeneration   → Image thumbnail generation
```

`Core` is referenced by all other projects. `Infrastructure` references only `Core`. `Albatross.Data` and `DataApi` are **alternative** `IDataSource` implementations — sites choose one via `services.AddEasywebDataSource()`.

## Namespace ↔ Folder Mapping

Folder names differ from assembly/namespace names. Always use the namespace form in code:

| Folder | Namespace / Assembly |
|---|---|
| `Easyweb.Core\` | `Easyweb.Site.Core` |
| `Easyweb.Infrastructure\` | `Easyweb.Site.Infrastructure` |
| `Easyweb.Site.DataApi\` | `Easyweb.Site.DataApi` |
| `Easyweb.ThumbnailGeneration\` | `Easyweb.Site.ThumbnailGeneration` |

## Component / Renderable System

CMS templates are rendered by **Renderable** classes. Inheritance hierarchy:

1. `IComponent<T>` → base interface: `TypeKey`, `CustomKey`, `FullKey`, `WebTemplate`
2. `Component<T>` → key resolution, parent/child tree
3. `Renderable` → `ViewName`, `CssClass`, rendering lifecycle (`Contextualize()` → `RenderAsync()`), `IRenderContext`
4. `RenderableTemplate<TOptions>` → deserializes `TOptions` from `WebTemplate.JsonOptions`
5. `ComponentBase<TOptions>` → adds `IActionResult` support for template routes

**Attribute-based dispatch** — components declare what they render via `[RendersFor]`:
```csharp
// Specific type+key → handles only FeatureType.Menu
[RendersFor(nameof(FeatureType), nameof(FeatureType.Menu))]
public class MenuFeature : RenderableTemplate<MenuTypeOptions> { ... }

// Type-only fallback → handles any FeatureType not claimed by a more specific class
[RendersFor(nameof(FeatureType))]
public class FeatureTemplate : RenderableTemplate { }
```
Type families: `FeatureType`, `ReferenceType`, `DisplayType`, `SectionType`, `ViewType`, `ModuleType`.

### Renderable Lifecycle

Override these methods in renderable subclasses:
- `Contextualize()` — load data, set state. Always call `base.Contextualize()` and guard with `if (IsContextualized) return this;`
- `RenderAsync(ViewContext)` — custom rendering logic after contextualization
- `SetOptions()` / `SetData()` — deserialize `TOptions` / content data from `WebTemplate.JsonOptions`
- `ShouldRender()` — return `false` to suppress output

### Template Options

Options classes live in `Easyweb.Core\Entities\Templates\TemplateOptions\` and inherit `TemplateOptions` (which inherits `ITemplateOptions`). Options are JSON-deserialized from `WebTemplate.JsonOptions`. Organize new options by type folder (e.g., `FeatureTypes\`, `ReferenceTypes\`).

## Data Access

- `IDataSource` — LINQ-style `Set<T>()` / `RawSet<T>()` + `TrySave/TryDelete/TryUpdate` operations
- `IContentDataProvider` / `IWebContentDataProvider` / `ISecurityDataProvider` — higher-level providers in `Easyweb.Core\Data\DataProviders\`
- `ICacheDataHandler` — caching decorator shared by both SQL and API data sources
- **Lifetimes**: `IDataSource` and data providers are **scoped**; query contexts and caches are **singleton**

## Request Pipeline

- `EasywebBaseController` — base controller; access `RequestContext`, `SiteContext`, `DataSource`, `DataProvider` via lazy properties (resolved from `HttpContext.RequestServices`)
- `IRenderContext` — central context passed to all renderables: data access, URL helpers, site options, template context, service provider
- `EasywebViewModel` — default model with `Article`, `Folder`, `Module`, `Linkable`, `SiteOptions`
- `ViewTemplatePage<TModel>` — base Razor page providing `Translate()`, `ArticleContext`, `ScopedModel`

### Routing

Routes registered in `AddEasywebRoutes()` in strict order: home → images/documents → module constraint routes → static pages → catch-all. Route keys: `culture`, `urlname`, `moduleRoute` (see `RouteConstants`).

## DI Conventions

- Entry point: `services.AddEasywebDefaults()` → calls `RegisterEasywebServices()` + `RegisterEasywebComponents()`
- Data source registration: `services.AddEasywebDataSource()` (defined separately in `Easyweb.Site.DataApi`)
- Service resolution in controllers/renderables uses lazy property pattern:
```csharp
protected IDataSource DataSource => _dataSource ??= HttpContext.RequestServices.GetRequiredService<IDataSource>();
```

## View Resolution

Templates are resolved from these paths (in `TemplateViewLocationExpander`):
1. `/Views/{moduleKey}/{0}.cshtml` (if module context exists)
2. `/Views/_Layout/{0}.cshtml` (if rendering layout)
3. `/Views/_Templates/{0}.cshtml`
4. `/Views/_Templates/_Sections/{0}.cshtml`
5. `/Views/_Templates/_Elements/{0}.cshtml`
6. `/Views/Shared/{0}.cshtml`
7. `/Views/_Default/{0}.cshtml`

## Key Resolution System (LocatorKey / ValueKey)

The `KeyResolver` is the engine behind template key expressions used in tag helpers. Keys are `$`-prefixed strings composed of **locator keys** (navigation) and **value keys** (data extraction), separated by `/` or `.`.

### Locator Keys — navigate the component tree
| Key | Resolves to |
|---|---|
| `$this` | Current renderable (default if omitted) |
| `$Article` | Closest article in the tree |
| `$Parent` | Parent renderable (skips list holder if child item) |
| `$Parents` | All ancestors — searched bottom-up |
| `$Folder` | Folder of the current linkable |
| `$Module` | Module of the current linkable |
| `$Layout` | The layout view (`TemplateContext.LayoutView`) |
| `$Settings` | The settings view (`TemplateContext.SettingsView`) |
| `$Page` | The page-level view (`TemplateContext.View`) |
| `$View` | Closest `View` renderable in the tree |
| `$MenuLayout` | Menu layout views |
| `$Linkable` | Current linkable entity |
| `$Template` | The `WebTemplate` of the current renderable |
| `$Content` | The `WebContent` of the current renderable |

### Value Keys — extract a property from the resolved target
`$Label`, `$Value`, `$HtmlValue`, `$Url`, `$Id`, `$Introduction`, `$Contents`, `$Caption`, `$Date`, `$Time`, `$Color`, `$Show`, `$OrderBy`, `$ChildCount`, `$Count`, `$Index`, `$Empty`, `$Created`, `$Edited`, `$Locked`, `$IsFolder`, `$ViewKey`

### Key syntax
- Combine with `/`: `$Parent/MyTemplateKey/$Value` → go to parent, find child "MyTemplateKey", get its value
- Shorthand: `$/` equals `$this/`, bare `$` equals `$this`
- Template keys (no `$` prefix) search children by `FullKey` match
- Reserved trailing chars: `<` (skip children), `*` (include references), `^`, `~`, `!`, `|`
- Sugar classes: `Locate` (alias for `LocatorKey`), `Property` (alias for `ValueKey`)

### Usage in tag helpers
```razor
@* Print article label *@
<span ew-for="$Article/$Label" />

@* Render a child template by key *@
<ew-template for-key="MyFeatureKey" />

@* Conditionally show if child exists *@
<div ew-if-key="Image">...</div>

@* Combine locator + template + value *@
<span ew-for="$Folder/BannerImage/$Url" />
```

### Programmatic use
```csharp
var resolver = new KeyResolver("$Parent/MyKey/$Label").Resolve(currentRenderable);
string label = resolver.StringValue;
bool exists = resolver.Exists;
```
Or with fluent chaining via `ComponentKeyResolver`:
```csharp
resolver.Then(Locate.Parent).FindTemplate("MyKey").Get(Property.Label);
```

## Configuration

Site configuration from `appsettings.json` sections:
- `SiteOptions` → `SiteOptions` class (debug, routing, images, paths, SEO, etc.)
- `ApiSettings` → `ApiSettings` class (UnionId, ClientId, ClientSecret for OAuth2)

Options classes inherit `BaseOption` (`Easyweb.Core\Options\BaseOption.cs`).

## Localization

JSON-based resource files in `/Resources`. Access via:
- `IJsonStringLocalizer` / `Localizer.Get(name, defaultContent, args)` in C#
- `<ew-translate />` tag helper in Razor views
- `ViewTemplatePage<T>.Translate(name, content, args)` in view code

## Build & Package

- Build: `dotnet build` from solution root

## View List Component

For ViewListComponent lists (arrays), use `ew-list="key"` on the wrapper element to loop out each item. `ew-template` alone doesn't iterate list items - `ew-list` is required for looping.
