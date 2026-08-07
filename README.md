# DAVx⁵ translations

This repository holds the **English source strings and all translations** of DAVx⁵. It contains no
code. [Weblate](https://hosted.weblate.org/projects/davx5/) reads and writes it directly, and the app
repositories pull it back in as a git submodule named `l10n`.

**Nothing in this repository is hand-edited.** See [Who writes what](#who-writes-what).

Want to help translating? → https://hosted.weblate.org/projects/davx5/

## How a string travels

```
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

Because the submodule is pinned to a commit, no translation reaches an app until somebody bumps that
pointer. That bump is the review step; it replaces the pull requests we used to get from Weblate.

## Layout

```
sources/ose/strings.xml                     English base, mirrored from davx5-ose
sources/gplay/strings.xml                   English base, mirrored from davx5
sources/managed/strings.xml                 English base, mirrored from davx5
sources/select/strings.xml                  English base, mirrored from davx5

translations/ose/values-de/strings.xml       one directory per language, per flavor
translations/gplay/values-de/strings.xml
translations/managed/values-de/strings.xml

.weblate-repo/values/strings.xml             Weblate bookkeeping — do not delete
```

There is no `translations/select/`: the select flavor has no Weblate component yet, so it has no
translations. `sources/select/strings.xml` is mirrored anyway, so that adding a component later needs
no repository changes.

## Who writes what

Each directory has **exactly one writer**. That is the whole design:

| directory | written by | never touched by |
|---|---|---|
| `sources/` | the mirror workflows in `davx5-ose` and `davx5` | Weblate, humans |
| `translations/` | Weblate | CI, humans |
| `.weblate-repo/` | nobody (see below) | everybody |

CI and Weblate therefore never write the same file, and a merge conflict between them cannot happen.
Weblate is configured with "Edit base file" **off**, which is what guarantees it can never write into
`sources/`.

If you hand-edit a file here, you break that guarantee — Weblate's clone and this repository start
diverging, and the conflicts and stuck components from
[#2627](https://github.com/bitfireAT/davx5-ose/issues/2627) come back.

**To change English text, edit it in `davx5-ose` (or `davx5` for gplay/managed/select).** The mirror
workflow copies it here on the next push to `main`.

To report a wrong translation, comment on the string in Weblate rather than editing the file.

## Rules for this repository

These exist because breaking them is what caused
[#2627](https://github.com/bitfireAT/davx5-ose/issues/2627). Please keep them even once nobody
remembers that issue.

- **Never squash-merge.** Weblate already produces one commit per translator, which is where the
  translator credits come from. Squashing collapses them into a single commit authored by a bot, and
  the attribution is gone for good.
- **Never rebase-merge.**
- **Never force-push `main`, and never delete it.** Weblate has its own clone of this repository. It
  pushes directly, so anything we rewrite is history it has already seen — after a rewrite its clone
  can no longer fast-forward, the components go "out of date" and lock themselves.
  (Both are also blocked by an organization ruleset, but don't go looking for a way around it.)
- **This repository must stay public.** The F-Droid build server clones it anonymously as a
  submodule. If it goes private, F-Droid builds silently ship an English-only app.
- **Don't delete `.weblate-repo/`.** It is the base file of Weblate's `repo-holder` component, which
  owns the shared git clone. Deleting that component deletes the components linked to it, together
  with their translation history, comments and suggestions. Details in the file itself.
- **Don't install the "Squash Git commits" Weblate add-on** on any component, for the same reason as
  the first rule.
- Adding a GitHub Actions workflow here needs a second pair of eyes. Weblate pushes with write
  permission, and GitHub's push rulesets — which could have restricted who may touch
  `.github/workflows/` — are not available for public repositories on our plan.

## Language directory names

The directory name is an Android resource qualifier, and Weblate maps it back to a language. Weblate
generates the name itself when a new language is added, so normally there is nothing to decide.

One name looks wrong but is correct: **Indonesian is `values-in`, not `values-id`.** Android uses the
legacy ISO 639-1 code here, and Weblate's Android string resource format maps `id` to `in`
accordingly. Please don't "fix" it.

## Related

- [#2627](https://github.com/bitfireAT/davx5-ose/issues/2627) — why this repository exists
- [Weblate project](https://hosted.weblate.org/projects/davx5/)
- [Weblate: avoiding merge conflicts](https://docs.weblate.org/en/latest/admin/continuous.html#avoiding-merge-conflicts-by-focusing-on-git-operations)

## License

GPL-3.0-or-later, see [LICENSE](LICENSE). Translations are contributed by the people listed in the
in-app credits screen.
