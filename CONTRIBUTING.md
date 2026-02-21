# Contributing a Plugin to ParkLife

This guide explains how to build a ParkLife plugin and submit it to the registry.

---

## How the Registry Works

The ParkLife plugin registry (`registry.json`) is a curated list of plugins that have been reviewed for safety and compatibility. When you submit a plugin, the registry team reviews your **listing** — your description, metadata, and README — not your source code. You own and maintain your plugin in your own GitHub repo.

This means:
- You move at your own pace
- Breaking changes in your plugin are your responsibility to document
- The registry team can remove a listing if a plugin becomes unmaintained or harmful

---

## Before You Build

Read the [Plugin API](#plugin-api) section below carefully. Plugins that import from undocumented core paths will break when ParkLife updates. Build only against the stable API surface.

Check the existing registry to make sure your idea isn't already covered. Duplicate plugins are fine but the registry won't list both unless they have meaningfully different approaches.

---

## Step 1 — Fork the Starter Template

Fork [parklife-plugin-template](https://github.com/SinCuri0/parklife-plugin-template) to your own GitHub account.

The template gives you:
- Correct folder structure
- Example component, hook, and types files
- A README template with all required sections
- An example database migration file

Rename the repo to something descriptive: `parklife-plugin-[yourpluginname]`

---

## Step 2 — Build Your Plugin

Your plugin lives entirely in `src/plugins/[your-plugin-name]/`. The required structure is:

```
src/plugins/your-plugin-name/
  README.md          ← required, see template
  registry.json      ← required, your plugin's own metadata
  types.ts           ← your TypeScript types
  constants.ts       ← your constants
  index.ts           ← public exports
  migrations/
    001_initial.sql  ← any required database changes
  components/        ← your React components
  hooks/             ← your custom hooks
```

### Rules

**What your plugin can do:**
- Add new React components and hooks
- Add new routes under `src/app/[your-plugin-name]/`
- Add new API routes under `src/app/api/[your-plugin-name]/`
- Add new database tables (via migrations in your `migrations/` folder)
- Add columns to existing tables if absolutely necessary (document this clearly)
- Import from the stable Plugin API (see below)
- Mount UI into the map page via the `HostControls` pattern (conditional render, no core modification)

**What your plugin cannot do:**
- Modify any file outside `src/plugins/[your-plugin-name]/` (except the one map page render point)
- Import from core paths not listed in the Plugin API
- Modify existing database tables' constraints or RLS policies
- Add routes that conflict with core routes (check the route table in the main spec)

---

## Plugin API

These are the only stable import paths your plugin should use:

```typescript
// Supabase clients
import { createClient } from '@/lib/supabase/client'
import { createServiceClient } from '@/lib/supabase/server'

// Types
import type { Pin, PinStatus, PinCategory, Group, GroupMember, Profile, Reaction, Comment, Notification } from '@/lib/types'

// Constants
import { MAP_DEFAULT_CENTER, GROUP_COLOURS, PIN_COLOURS, PIN_CATEGORY_ICONS, PIN_CATEGORY_LABELS, REACTION_EMOJIS } from '@/lib/constants'

// Base map component
import BaseMap from '@/components/BaseMap'
```

### Supabase Realtime

Subscribe to any table using the standard Supabase pattern. Filter by the fields relevant to your plugin. Unsubscribe on component unmount.

### Database

Your plugin may read from any existing table that RLS allows. Your plugin may write to tables where RLS allows it. If you need new tables, include SQL migrations in your `migrations/` folder. Number them sequentially: `001_initial.sql`, `002_add_column.sql` etc.

---

## Step 3 — Write Your README

Your plugin's README is what users and the registry team will read. It must include:

**What it does** — one paragraph, plain language, no jargon.

**What it adds to the database** — list every table and column your migrations create or modify.

**What it imports from core** — list every `@/lib/*` and `@/components/*` import.

**Installation steps** — numbered, exact. Assume the reader is a developer but not familiar with your plugin.

**Routes** — every route your plugin adds, in a table.

**Known limitations** — be honest. If it doesn't work on mobile, say so.

Use the README template from the starter repo as your starting point.

---

## Step 4 — Test It

Before submitting, verify:

- All your routes load without errors
- Your database migrations run cleanly on a fresh Supabase project
- No TypeScript errors (`npm run build` passes)
- Existing ParkLife functionality is unaffected by your plugin
- Your README installation steps work when followed exactly

---

## Step 5 — Submit to the Registry

1. Fork this repo (`parklife-plugins`)
2. Add your plugin entry to `registry.json`:

```json
{
  "id": "your-plugin-id",
  "name": "Your Plugin Name",
  "description": "One or two sentences. What does it do and why would someone want it.",
  "author": "your-github-username",
  "repo": "https://github.com/your-username/parklife-plugin-yourplugin",
  "plugin_path": "src/plugins/your-plugin-name",
  "version": "0.1.0",
  "parklife_version_min": "0.1.0",
  "tags": ["one", "or", "two", "tags"],
  "requires_migrations": true,
  "official": false
}
```

3. Update the plugin table in `README.md`
4. Open a pull request using the PR template

The PR template will ask you to confirm:
- Your plugin follows the Plugin API rules
- Your README is complete
- Your migrations are reversible or clearly documented
- You commit to maintaining the plugin or flagging it as unmaintained

---

## Review Process

Registry PRs are reviewed by @SinCuri0. Typical turnaround is within a week. Feedback will be left as PR comments. Once merged, your plugin appears in the registry immediately.

Reasons a listing might be rejected:
- Imports from undocumented core paths
- Incomplete README
- Malicious or harmful functionality
- Duplicate of an existing plugin without meaningful differentiation

Rejection is not permanent — fix the issues and resubmit.

---

## Maintaining Your Plugin

You are responsible for keeping your plugin working as ParkLife updates. The `parklife_version_min` field in registry.json indicates the minimum ParkLife version your plugin supports. Update this when you make breaking changes.

If you can no longer maintain a plugin, open an issue in this repo and we'll mark it as unmaintained in the registry.

---

## Questions

Open an issue in this repo before building if you're unsure whether your idea fits the plugin model or conflicts with planned core features.
