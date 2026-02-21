# ParkLife Plugin Registry

The official registry of community-built plugins for [ParkLife](https://github.com/SinCuri0/parklife) — the open source community map platform.

Plugins extend ParkLife without modifying the core app. They live in their own folders, own their own routes, and are installed by community admins per-group.

---

## Available Plugins

See [registry.json](./registry.json) for the full machine-readable list.

| Plugin | Description | Author |
|---|---|---|
| [vicarious](https://github.com/SinCuri0/parklife) | Live challenge game mode for group admins and members | @SinCuri0 |

---

## Installing a Plugin

Plugins are installed into your ParkLife instance by copying the plugin folder into `src/plugins/` in your codebase.

Each plugin's README contains full installation instructions including any required database migrations.

---

## Building a Plugin

Read [CONTRIBUTING.md](./CONTRIBUTING.md) for the full guide.

The short version:

1. Fork the [plugin starter template](https://github.com/SinCuri0/parklife-plugin-template)
2. Build your plugin following the ParkLife Plugin API
3. Publish it to your own GitHub repo
4. Submit a PR to this registry adding your entry to `registry.json`

The registry team reviews your listing — not your code. You own and maintain your plugin.

---

## Plugin API

Plugins may import from the following core ParkLife paths:

```typescript
@/lib/supabase/client     // Browser Supabase client
@/lib/supabase/server     // Server Supabase client (service role)
@/lib/types               // Shared TypeScript types
@/lib/constants           // Map defaults, colours, category labels
@/components/BaseMap      // Base Mapbox map component
```

Do not import from any other core path. Anything not listed here may change without notice.

---

## Questions

Open an issue in this repo. For bugs in a specific plugin, open an issue in that plugin's own repo.
