---
layout: default
title: Micro-languages
description: Small programming language definitions for teaching compiler construction.
---

# Micro-languages

A collection of small language definitions I created for teaching compiler
construction at the undergraduate level, and for hobbyists who want to write a
compiler of their own. Each one is small enough to implement in a term and
rich enough to exercise the main compiler phases.

By design, each definition contains omissions, ambiguities or errors that the
implementer is expected to discover and resolve.

## The languages

<table>
  <thead>
    <tr><th>Language</th><th>Summary</th><th>Audience</th><th>Target</th><th>Available in</th></tr>
  </thead>
  <tbody>
  {% for l in site.data.languages %}
    <tr>
      <td><a href="{{ l.url | relative_url }}">{{ l.name }}</a></td>
      <td>{{ l.summary }}</td>
      <td>{{ l.audience }}</td>
      <td>{{ l.target }}</td>
      <td>{% for a in l.available %}<a href="{{ a.url | relative_url }}">{{ a.label }}</a>{% unless forloop.last %}, {% endunless %}{% endfor %}</td>
    </tr>
  {% endfor %}
  </tbody>
</table>

## About these definitions

Small-C and Micro-C are two versions of a small C language and are mostly
equivalent. The one I called Small-C has no relation to the famous book and
implementation of the same name. The difference between them is mostly in the
documentation I wrote to define them.

## Feedback

Questions, errata and reference implementations are welcome as
[issues](https://github.com/efutch/Small-C/issues).

## License

These definitions are licensed under
[Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/)
(CC BY-SA 4.0). You may share and adapt them, with credit and under the same license.
