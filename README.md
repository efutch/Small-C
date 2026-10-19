# Micro-languages

A collection of small programming language definitions, useful for teaching compiler design at the undergrad level, or for hobbyists.

Live site: <https://efutch.github.io/Small-C/>

| Language | Folder | Page |
|---|---|---|
| Small-C | [`small-c/`](small-c/index.md) | <https://efutch.github.io/Small-C/small-c/> |
| Micro-C (English) | [`micro-c/`](micro-c/index.md) | <https://efutch.github.io/Small-C/micro-c/> |
| Micro-C (Español) | [`micro-c/es.md`](micro-c/es.md) | <https://efutch.github.io/Small-C/micro-c/es/> |

Small-C and Micro-C are mostly equivalent versions of a small C language; Small-C has no relation to the famous book and implementation of the same name. The difference is mostly in the documentation.

## Adding a language

1. Create a folder with an `index.md` that has front matter (`layout`, `title`, `description`, `permalink`).
2. Add an entry to [`_data/languages.yml`](_data/languages.yml). The landing page picks it up automatically.
