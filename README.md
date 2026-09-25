# Magic website template

A small, mostly empty website for Magic Cloud. It is a starting point, not a
product: four pages, a shared navigation bar, a blog that reads Markdown files,
and enough styling that it does not look unfinished while you replace it.

The content is a running joke about a consultancy that does the paperwork for
spies. Replace it with your own — that is the point.

## Installing

This is a **frontend** plugin. Installing it unzips the repository straight
into `/etc/www/`, so the repository root *is* the web root.

## What is here

```
index.html   index.hl           /
about.html   about.hl           /about        copy these two to add a page
blog.html    blog.hl            /blog         the roll
blog/default.html                             /blog/<slug>
blog/default.hl                               that page's own furniture
blog/interceptor.hl                           finds the article
.components/ navbar.html, footer.html, article-card.html
.blog/       one article, as Markdown
assets/      styles.css
```

The roll is `blog.html` at the root rather than `blog/index.html`, so that the
interceptor inside `blog/` applies to articles only.

## How a page is put together

Magic serves an HTML file by evaluating the Hyperlambda file **beside it with
the same name**, then replacing every `{{expression}}` in the markup with what
that expression returns. `index.html` says `{{*/.navbar}}`, and `index.hl` has
a `.navbar` node that loads `.components/navbar.html` and returns it. That is
the whole mechanism.

Three rules about it are worth knowing before you go further, because each one
fails in a way that looks like something else:

**A page needs its own `.hl` to be dynamic at all.** With no code-behind beside
it, an HTML file is served flat and its `{{…}}` are shown to the reader as
text. An interceptor does not change this.

**A code-behind is not executed.** Its nodes are *invoked*, one at a time, by
the expressions that name them — so statements written at the top level of a
page's `.hl` never run. Anything that has to happen before the page is filled
in belongs in an `interceptor.hl`, which *is* executed, top to bottom.

**The two forms reach different files, and neither reaches the other.**

| in the markup | finds a node in |
|---|---|
| `{{*/.name}}` | the page's own code-behind |
| `{{@.name}}`  | an `interceptor.hl` |

`blog/default.html` uses both, and which form each value uses tells you where
it comes from: the navigation bar from `default.hl`, the article itself from
`interceptor.hl`.

Files starting with a dot are not served. `https://your-site/.components/navbar.html`
is a 404, which is why fragments live there.

### Adding a page

Copy `about.html` and `about.hl`, rename both, and change `current:about` in
the `.hl` to a name you also add to `.components/navbar.html`. The active-link
styling is three lines in `assets/styles.css`; add yours next to them.

A page with **no** `.hl` beside it is served as a plain static file. If your
`{{…}}` are showing up as text on the page, that is why.

### Adding an article

Write a Markdown file into `.blog/`, named `YYYY-MM-DD-slug.md`:

```markdown
---
title: Your exfiltration plan needs a boat
date: 2026-09-20
author: Margaret Pemberton
description: One sentence, used on the card and as the page description.
---

The article, in Markdown.
```

The date in the filename orders the roll — newest first — and the slug after it
is the address: `2026-09-20-boats.md` is served at `/blog/boats`. Nothing else
needs updating. There is no index and no database.

## Editing it

Both Chat Ops and the Web Designer work on these files directly.

In the Web Designer, `{{…}}` appears as a grey chip rather than as text, so you
can see where the server fills something in and will not type over it by
accident. The navigation bar and footer are chips on every page — to change
them, open `.components/navbar.html` itself.

## Licence

Do whatever you like with it.
