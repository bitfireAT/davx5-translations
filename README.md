# DAVx⁵ translations

Want to help translating? → https://hosted.weblate.org/projects/davx5/

This repository holds the **English source strings and all translations** of DAVx⁵. The other DAVx⁵ repositories mirror their english original strings over in to `/sources`. [Weblate](https://hosted.weblate.org/projects/davx5/) reads the original strings and writes only to `/translations`. The other DAVx⁵ repositories pull this whole repository back in as a git submodule named `l10n`.

## How a string travels

```text
English text is written in the app repos
  davx5-ose:  core/src/main/res/values/strings.xml
  davx5:      app-non-ose/src/{gplay,managed,select}/res/values/…
        │
        │  a GitHub Actions workflow in the app repo mirrors the file over
        ▼
  sources/<flavor>/strings.xml            (this repo)
        │
        │  Weblate reads this as the English base
        ▼
  Weblate — translators work here
        │
        │  Weblate pushes directly to main, no pull request
        ▼
  translations/<flavor>/values-*/strings.xml   (this repo)
        │
        │  git submodule "l10n", pinned to one commit
        ▼
  davx5-ose  ──▶ APK  (F-Droid)
  davx5      ──▶ APK  (Google Play, Managed DAVx⁵)  — via davx5-ose/l10n
```

Because the submodule is pinned to a commit, no translation reaches the DAVx⁵ App until the pointer is bumped.

## Nothing here is hand-edited

Every directory has exactly one writer: `sources/` is written by the mirror workflows, `translations/` by Weblate, `.weblate-repo/` by nobody. That is why CI and Weblate can never conflict.

* **To change English text**, edit it in `davx5-ose` (or `davx5` for gplay/managed/select). The mirror workflow copies it here.
* **To report a wrong translation**, comment on the string in Weblate instead of editing the file.

## Rules

* **Never squash- or rebase-merge**, and don't install the "Squash Git commits" Weblate add-on. Weblate makes one commit per translator — that's where translator credits come from.
* **Never force-push or delete `main`**. Weblate has its own clone and can no longer fast-forward after a rewrite; its components lock themselves.
* **Keep this repository public.** The F-Droid build server clones it anonymously; if it goes private, F-Droid silently ships an English-only app.
* **Don't delete `.weblate-repo/`**. It is the base file of Weblate's `repo-holder` component; deleting it takes the linked components and their translation history with it.

## Notice

Weblate generates these Android resource qualifiers itself. One looks wrong but is correct: **Indonesian is `values-in`**, not **`values-id`** (Android uses the legacy ISO 639-1 code). Please don't "fix" it.

## License

GPL-3.0-or-later, see LICENSE. Translations are contributed by the people listed in the in-app credits screen.
