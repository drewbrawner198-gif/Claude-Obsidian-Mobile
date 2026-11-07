# Android home-screen access to the Task Dashboard

Goal: get from the home screen to the Task Dashboard in one tap, every time,
with an icon that sits naturally among the apps. If possible, also put a large
live preview (up to 5×6) of the dashboard on the home screen.

The setup has three tiers. Each one works without the next, so stop wherever
you're happy.

| Tier | Result | Tools | Effort |
|---|---|---|---|
| 1 | Tapping always lands on the dashboard | Advanced URI + Homepage (community plugins) | 5 min |
| 2 | A clean 1×1 app-style icon for it | Shortcut Maker (Play Store) | 5 min |
| 3 | A large, resizable live task widget | TaskForge (Play Store), or Markdown Widget | 10–20 min |

> Placeholders used below. Replace them with your own values:
> - `VAULT`: your vault's name exactly as Obsidian shows it (e.g. `Tasks`)
> - `DASHBOARD`: the dashboard's path inside the vault, without `.md`
>   (e.g. `Tasks Dashboard` or `00 Home/Task Dashboard`)
>
> In URIs, encode spaces as `%20` and `/` as `%2F`.
> `00 Home/Task Dashboard` becomes `00%20Home%2FTask%20Dashboard`.

---

## Why the built-in widget misbehaves

Obsidian's own **Open Note** widget is static (no preview), and its icon can't
be changed. Its behaviour also depends on whether Android kept Obsidian in
memory:

- **Cold start:** Obsidian restores your last workspace, and the widget's
  request to open a note can be lost while the app loads.
- **Warm resume:** the note opens in whichever tab was last active, and it
  can look as if nothing happened.

Tier 1 fixes both cases: one plugin catches every app start, and an explicit
URI handles every resume.

---

## Tier 1: Make it always land on the dashboard

### 1a. Install the **Homepage** plugin (by mirnovov)

Settings → Community plugins → Browse → "Homepage" → Install → Enable.

Settings → Homepage:

- **Homepage:** `DASHBOARD`
- **Open on startup:** on
- **Open mode:** "Replace all open notes". This keeps old tabs from piling
  up on mobile.
- **Use when opening normally:** on (if your version has it)
- Optional: **Show ribbon/toolbar button** so there's a one-tap "go home" inside the app.

This covers every cold start. Even the stock Obsidian icon will now land on
the dashboard after Android has closed the app.

### 1b. Install the **Advanced URI** plugin (by Vinzent03)

Settings → Community plugins → Browse → "Advanced URI" → Install → Enable.

This gives you a link that **focuses** the dashboard if it's already open in a
tab, or opens it if it isn't. That covers the warm-resume case:

```
obsidian://adv-uri?vault=VAULT&filepath=DASHBOARD.md&openmode=false
```

- `openmode=false` opens the note in the current tab if it isn't already
  open, and focuses its tab if it is. If you'd rather keep the current note
  and open the dashboard in a new tab, use `openmode=true`.
- Fallback without the plugin (core Obsidian):
  `obsidian://open?vault=VAULT&file=DASHBOARD`
- Your exact URI: open the dashboard → ⋮ menu (or command palette) →
  **"Advanced URI: copy URI for current file"**. This gets the encoding right.

**Test it before Tier 2:** paste the URI into a note, open a *different* note,
switch to another app, then tap the link from a Google Keep note or a message
to yourself. You should land on the dashboard.

---

## Tier 2: A real app-style icon

Use **Shortcut Maker** (Rushikesh Kamewar, Google Play). It's free, has been
around for years, and is widely used. It creates a normal launcher shortcut
with any icon you like.

1. Open Shortcut Maker → **Websites** (also called "URL" in some versions).
2. Paste the Tier 1b URI (`obsidian://adv-uri?...`).
   If the app adds `http://` in front of it, delete it.
3. **Label:** e.g. `Tasks`.
4. **Icon:** pick one of:
   - an **icon pack** you already use, so it matches your other apps
     (search the pack for "tasks", "checklist", "todo"),
   - a **built-in icon** with a colored background,
   - an image (the Obsidian logo, or a checkbox glyph).
5. **Create shortcut** → place it. On first tap Android may ask which app
   opens it. Choose **Obsidian** → "Always".

**If tapping does nothing,** use Shortcut Maker's **Activities/Intent** mode
instead: pick *Obsidian* → main activity, set **Action** to
`android.intent.action.VIEW`, put the URI in **Data**, then create the shortcut.

Alternatives, if you already use them:

- **Nova / Lawnchair / Niagara / Smart Launcher:** long-press any shortcut →
  Edit → change the icon. (Pixel Launcher and One UI Home can't change icons,
  which is why this guide uses Shortcut Maker.)
- **HTTP Shortcuts** (open source, Play Store) can do the same with a
  "Browser shortcut" of type *Open with app*.

Once this works, remove Obsidian's broken-note widget.

---

## Tier 3: A large live task widget (up to 5×6)

No third-party widget can *render* Obsidian plugins. A dashboard built from
```` ```tasks ```` or ```` ```dataview ```` blocks shows up in a generic
Markdown widget as **raw query code**, not as your tasks. Choose the option
that matches how your dashboard is built.

### Option A (recommended): TaskForge (Google Play, "TaskForge – Obsidian Tasks")

TaskForge reads the Markdown files in your vault and parses Tasks-plugin
syntax itself (`- [ ]`, 📅 due dates, ⏫ priority and so on). That makes it the
only Play Store option that can show a *live* list of your tasks, not the
dashboard's raw query text.

1. Install → choose your **vault folder** when asked.
   - It needs Android's **"All files access"** permission to read the vault.
     This is broad access. It's the same trade-off every vault-reading widget
     makes. Grant it only if you're comfortable with that.
2. Set up a list or filter in TaskForge that mirrors your dashboard
   (e.g. *Due today + overdue*, *High priority*, *Inbox*).
3. Home screen → long-press → Widgets → **TaskForge** → drag out and resize
   up to 5×6.
4. Check in-app: some widget features are part of the paid tier. The app is
   free to download, and premium is a subscription or a one-time purchase.

Keep the Tier 2 icon next to it (or inside a folder) for jumping into the full
dashboard.

### Option B: Markdown Widget (open source, by Tiim)

This one is **not on the Play Store**. Get it from **F-Droid** or
**IzzyOnDroid**. It shows a single Markdown file as a resizable widget, and
it's made with Obsidian vaults in mind.

It only works well if the file holds **plain tasks, not queries**. Two ways
to get that:

- Keep a hand-maintained `Today.md` or `Inbox.md` with literal `- [ ]` lines,
  and point the widget at it.
- Or install the **Dataview Serializer** community plugin (by dsebastien). It
  writes Dataview query results into the note as plain Markdown on every save.
  Point the widget at that note. It only handles queries Dataview can express,
  and it updates only when Obsidian runs, so check after installing that your
  query type is supported.

The widget is read-only. Tapping it opens the file, and you can point that at
Obsidian.

### Either option: the vault must be readable by other apps

This depends on how the vault syncs to the phone:

| Vault location | Readable by widget apps? |
|---|---|
| `Documents/…` or another shared-storage folder (Syncthing, FolderSync, Git, or Obsidian Sync into a folder you picked) | ✅ Yes |
| `Android/data/md.obsidian/…` (app-private) | ❌ No. Move the vault to `Documents/` (Obsidian → Manage vaults → create in a shared folder, then sync into it) |

---

## Quick checklist

- [ ] Homepage plugin installed, homepage = dashboard, open on startup
- [ ] Advanced URI installed; copied the dashboard's URI
- [ ] Tested the URI from outside Obsidian, from both cold start and resume
- [ ] Shortcut Maker shortcut with a matching icon placed on the home screen
- [ ] Removed Obsidian's stock Open Note widget
- [ ] (Optional) TaskForge widget resized to the space you want
