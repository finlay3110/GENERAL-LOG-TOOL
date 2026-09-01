# Restyle prompt — UCN console look

Paste everything below the line into an LLM along with the app you want restyled.
Fill in the two `<...>` placeholders first.

The CSS in here is lifted verbatim from the UCN General Log, so it is known to
work rather than approximated.

---

You are restyling an existing app to match a house visual style. Below is the
style, the two components I care most about, and a hard list of things you must
not break.

**The app:** `<NAME + ONE LINE ON WHAT IT DOES>`

**The code:** `<PASTE THE FILE(S), OR NAME THEM IF THEY ARE ALREADY IN CONTEXT>`

## Rule zero — restyle, do not rewrite

This is a visual change. Keep the existing behaviour, data model, storage format,
element ids, event handlers and function names unless a change is strictly forced
by the markup. Do not "modernise" logic you were not asked about, do not swap
frameworks, do not reorganise files.

**Do not remove any feature. In particular the app has quick-entry buttons — one-tap
buttons that log something immediately. They stay.** Restyle them (there is a
section on exactly how below), never delete, merge or bury them behind a menu. If
you think a quick-entry button conflicts with the style, say so in your summary and
leave it working; do not resolve it by deleting it.

At the end, list anything you changed structurally and anything you deliberately
left alone.

## The feel

A naval bridge console: dark, high-contrast, navy and amber. Used one-handed on a
phone, in a darkened room, mid-session, by someone who is also doing something else.
Dense and functional, not airy. Mobile-first — design for ~414×896, then let the
layout centre with a `max-width` around 520px rather than building a desktop layout.

Dark only. Never add a light theme or a theme switcher.

## Palette

```css
:root{
  color-scheme: dark;
  --navy:#1B2A5E;  --navy-dark:#111a3d;  --orange:#DD7A2B;  --red:#B23A3A;
  --white:#ffffff;
  --bg:#0b0f1c;    --bg2:#0f1730;
  --card:#141c38;  --card-hi:#1b254a;    --border:#2a3a66;
  --text:#e8ecf7;  --muted:#8c98bf;
  --safe-top: env(safe-area-inset-top, 0px);
  --safe-bottom: env(safe-area-inset-bottom, 0px);
}
```

Orange is the primary-action colour: **one solid orange call-to-action per screen**,
plus section headings. Red is for destructive actions and for critical/alert state —
nothing else.

## Typography

Orbitron for anything structural — app title, section headings, badges, nav labels,
clocks, timestamps. Always uppercase, letter-spacing 0.04–0.10em. Exo 2 for body
text, inputs and paragraphs.

```css
--font-display:'Orbitron', 'Exo 2', system-ui, sans-serif;
--font-body:'Exo 2', system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
```

Small and dense: 9–10px uppercase labels, 13–14px body, 15px app title. If the app
already loads fonts, keep its loading strategy and just point these two variables at
Orbitron and Exo 2; do not introduce a font CDN into an app that does not already
use one.

## Background — both layers, they do most of the work

```css
body{
  background:radial-gradient(ellipse at top, #101a38 0%, var(--bg) 55%);
  overscroll-behavior:none;
}
body::before{                       /* faint amber graph-paper grid */
  content:''; position:fixed; inset:0; pointer-events:none; z-index:0;
  background-image:
    linear-gradient(rgba(221,122,43,0.025) 1px, transparent 1px),
    linear-gradient(90deg, rgba(221,122,43,0.025) 1px, transparent 1px);
  background-size:28px 28px; opacity:0.5;
}
```

Content sits at `z-index:1`. If the result feels flat despite the colours matching,
it is because one of these two layers is missing.

## Section headings

```css
.sec{
  font-family:var(--font-display); font-size:13px; letter-spacing:0.1em;
  text-transform:uppercase; color:var(--orange); font-weight:700;
  margin:18px 0 8px; padding-bottom:6px; border-bottom:1px solid var(--border);
}
.sec-sub{ font-size:11px; color:var(--muted); margin:-4px 0 10px; }
```

The optional sub-line under a heading is worth using. It is where you say the thing
the user would otherwise have to guess — "Timestamped the moment you save it", or
who the entry will be filed under.

## Fields

```css
.f{ margin-bottom:10px; }
label{
  display:block;
  font-size:10px; text-transform:uppercase; letter-spacing:0.06em;
  color:var(--muted); font-weight:600; margin-bottom:3px;
}
input, select, textarea{
  width:100%; background:rgba(255,255,255,0.05); border:1px solid var(--border);
  color:var(--text); border-radius:9px; padding:10px; font-size:14px;
  font-family:var(--font-body);
  transition:border-color .2s, background-color .2s;
}
input:focus, select:focus, textarea:focus{
  outline:none; border-color:var(--orange); background:rgba(221,122,43,0.08);
}
textarea{ resize:vertical; min-height:70px; line-height:1.5; }
select option{ color:#16204a; background:#ffffff; }
.row{ display:flex; gap:8px; }
.row > .f{ flex:1; min-width:0; }
```

`select option` needs that explicit colour or the options render unreadable on some
platforms. Two fields side by side go in a `.row`.

# Component 1 — the compose card

This is the "write a new entry" block. Anatomy, top to bottom:

1. A section heading (`NEW ENTRY`) with a muted sub-line saying what happens on save.
2. **One card** holding every input: the classifying select(s), then the textarea,
   then any toggles. Nothing else — no buttons inside the card.
3. **The solid orange primary button sits below the card, not inside it.** That
   separation is the point: the card is the form, the button is the commitment.

```css
.card{
  background:linear-gradient(160deg, var(--card) 0%, var(--card-hi) 100%);
  border:1px solid var(--border); border-radius:14px; padding:12px 12px 10px;
  margin-bottom:10px;
}
.btn-primary{
  display:block; width:100%; border:0; cursor:pointer;
  background:var(--orange); color:#241100;
  border-radius:16px; padding:22px 14px; font-size:17px; font-weight:700;
  font-family:var(--font-body);
  box-shadow:0 6px 22px rgba(221,122,43,0.30);
  letter-spacing:0.02em;
}
.btn-primary:active{ background:#c96b22; }
.btn-primary[disabled]{ opacity:0.45; box-shadow:none; cursor:default; }
```

The button is deliberately tall (22px vertical padding). It is the one thing on the
screen that must be hittable without looking.

Toggle switch for any boolean in the card — 46×26 pill, white 20px knob, orange when
on. Use `role="switch"` with `aria-checked` kept in sync, not a bare div.

```css
.sw-row{ display:flex; align-items:center; gap:10px; margin:6px 0 10px; }
.sw-row .sw-lb{ flex:1; min-width:0; font-size:13px; }
.sw-row .sw-lb small{ display:block; font-size:11px; color:var(--muted); }
.sw{
  flex:0 0 46px; width:46px; height:26px; border-radius:13px; cursor:pointer;
  background:rgba(255,255,255,0.10); border:1px solid var(--border);
  position:relative; padding:0; transition:background-color .18s;
}
.sw::after{
  content:''; position:absolute; top:2px; left:2px; width:20px; height:20px;
  border-radius:50%; background:var(--white); transition:transform .18s;
}
.sw[aria-checked="true"]{ background:var(--orange); border-color:var(--orange); }
.sw[aria-checked="true"]::after{ transform:translateX(20px); }
```

On save: clear the input, reset the toggles, and confirm with a toast — never an
`alert()`. Toast is an orange pill above the bottom chrome, `#241100` text, radius
24px, fades up over 0.25s, gone after ~1.8s. Put something useful in it: not "Saved"
but "Logged at 21:14".

# Component 2 — the log block

One record, one card. Anatomy:

- **Head row:** a badge on the left naming the record's type, a flexible spacer, the
  timestamp on the right (Orbitron, orange, with the date beneath it in 9px muted),
  then a 30px round red delete button.
- **Body:** the text itself at 13.5px, `white-space:pre-wrap`, no truncation.
- **Foot row:** a spacer, then a small quiet Edit button on the right.

```css
.card-head{ display:flex; align-items:center; gap:8px; margin-bottom:9px; }
.card-head .grow{ flex:1; min-width:0; }
.badge{
  font-family:var(--font-display); font-size:12px; font-weight:700;
  letter-spacing:0.05em; text-transform:uppercase;
  color:var(--white); background:var(--navy); border:1px solid var(--orange);
  border-radius:7px; padding:4px 9px; max-width:100%;
  white-space:nowrap; overflow:hidden; text-overflow:ellipsis;
}
.del{
  flex:0 0 30px; width:30px; height:30px; border-radius:50%;
  background:rgba(178,58,58,0.18); border:1px solid var(--red); color:#ff9d9d;
  font-size:14px; line-height:1; cursor:pointer; padding:0;
  display:flex; align-items:center; justify-content:center;
}
.ent-time{
  font-family:var(--font-display); font-size:12px; font-weight:700;
  letter-spacing:0.06em; color:var(--orange); white-space:nowrap;
}
.ent-time small{ display:block; color:var(--muted); font-size:9px; letter-spacing:0.08em; }
.ent-tx{ font-size:13.5px; line-height:1.5; white-space:pre-wrap; word-wrap:break-word; margin:0; }
.ent-foot{ display:flex; gap:8px; align-items:center; margin-top:9px; }
.ent-foot .grow{ flex:1; min-width:0; }
.ent-foot .btn-quiet{ margin:0; padding:7px 10px; font-size:12px; width:auto; border-radius:9px; }
```

Newest first. Where two records share a timestamp, break the tie on insertion order
so the later one still sorts first — a plain string compare will silently put them
the wrong way round.

## Flagged / critical state

When a record is flagged, make it unmissable rather than tasteful: a full-bleed red
banner across the top of the card, a 2px red border, and a red-tinted body.

```css
.card.crit{
  border:2px solid var(--red); padding:11px 11px 9px;
  background:linear-gradient(160deg, #2b1526 0%, #35202f 100%);
  box-shadow:0 0 0 1px rgba(178,58,58,0.35), 0 4px 18px rgba(178,58,58,0.22);
}
.crit-bar{
  display:flex; align-items:center; gap:7px;
  margin:-11px -11px 10px; padding:7px 11px;
  background:var(--red); color:var(--white); border-radius:11px 11px 0 0;
  font-family:var(--font-display); font-size:10px; font-weight:700;
  letter-spacing:0.1em; text-transform:uppercase;
}
.crit-bar .ic{ font-size:12px; line-height:1; }
.card.crit .badge{ border-color:#ff9d9d; }
```

The negative margins pull the banner out to the card edge; they must match the
`.card.crit` padding, and the banner's top radius must be the card radius minus the
border width.

# Component 3 — the quick-entry buttons (keep these)

The app's one-tap quick-entry buttons are the fastest path in the whole interface.
They keep their behaviour exactly; only their look changes.

Give them the dashed-orange treatment, in a grid directly under the compose card's
primary button, under their own section heading or a muted label:

```css
.quick{ display:grid; grid-template-columns:repeat(2, 1fr); gap:8px; margin-top:10px; }
.quick button{
  cursor:pointer; background:rgba(221,122,43,0.12); border:1.5px dashed var(--orange);
  color:var(--orange); border-radius:12px; padding:12px 10px;
  font-family:var(--font-body); font-size:14px; font-weight:600;
  min-height:48px; text-align:center;
}
.quick button:active{ background:rgba(221,122,43,0.22); }
```

Two per row at phone width; drop to one column if the labels are long, or three if
they are one word. Never below a 44px tap target.

Why dashed and not solid: the solid orange primary button is the single loud
call-to-action, and the rule is one per screen. Dashed-orange reads as the same
family — "this also adds a record" — while keeping the hierarchy legible. If a quick
button emits an emoji, put it before the label at 16px; the label still carries the
meaning, so it must read fine with the emoji ignored.

Every quick-entry tap fires a toast naming what was logged, because the record it
creates may be below the fold.

## Interaction rules

- `*{ -webkit-tap-highlight-color:transparent }` kills the touch flash — but you MUST
  then add `:focus-visible{ outline:2px solid var(--orange); outline-offset:2px }` or
  keyboard focus becomes invisible.
- Modals are **bottom sheets**, not centred dialogs: full width, `max-width:520px`,
  `max-height:88vh`, `border-radius:18px 18px 0 0`, 2px orange top border, on an
  `rgba(8,12,24,0.72)` scrim. Close on Escape and backdrop click; move focus into the
  sheet on open and back to the trigger on close; `role="dialog"`, `aria-modal`,
  `aria-labelledby`.
- Collapsible sections carry `aria-expanded`/`aria-controls` kept in sync, with a `▾`
  chevron that rotates 180° when open.
- Empty states always say something in italic muted text ("None recorded."), never
  blank space.
- Destructive actions confirm first, in a sheet with a red bordered banner.
- Anything that can silently lose data says so loudly — a red bordered banner, not a
  muted line.
- Icons are emoji, sparse and functional (📋 👥 🗒️ ⏱️ 🎖️ 🔧 🧭 ⚡ ☢️). Never where a
  word is clearer.

## Gotchas that will bite you

- **`[hidden]` loses to any class that sets `display`.** `.crit-bar` sets
  `display:flex`, so toggling the `hidden` attribute on it does nothing and every
  record renders as flagged. Add `[hidden]{ display:none !important; }` once,
  globally, and toggle visibility with the attribute rather than inline styles.
- Set user-supplied text with `textContent`, never `innerHTML`. Use `innerHTML` only
  for static templates you wrote, then fill the values in afterwards.
- If a fixed header can change height, publish it as a CSS variable
  (`--header-h`) and re-measure on resize, on orientation change, and whenever
  anything in the header shows or hides. Do not hard-code the padding.
- Respect `--safe-top` / `--safe-bottom` on all fixed chrome.
- Honour `prefers-reduced-motion` by dropping the panel transitions.

## Do not

- Add a light mode or a theme switcher.
- Add hero sections, generous whitespace or marketing layout. This is a console.
- Use pill-rounded "friendly app" corners. Corners are 7–16px, except toasts (24px)
  and the toggle switch.
- Put more than one solid orange call-to-action on a screen.
- Add a network request of any kind — no font CDN, no icon CDN, no analytics.
- Remove, merge or hide the quick-entry buttons.

## Acceptance checklist

Before you answer, confirm each of these:

- [ ] Every quick-entry button that existed before still exists, still fires the same
      handler, and is reachable in one tap from the same screen.
- [ ] No feature, handler or stored field was removed.
- [ ] Exactly one solid orange call-to-action per screen.
- [ ] Both background layers present; content above them at `z-index:1`.
- [ ] `[hidden]{display:none !important}` present, and every conditional element
      actually hides.
- [ ] Focus is visible on keyboard navigation.
- [ ] Flagged records are obvious at a glance while scrolling past.
- [ ] Empty states read as italic muted sentences.
- [ ] Nothing is loaded over the network that was not before.

Then summarise: what you restyled, what you changed structurally and why, and
anything you left alone because touching it would have risked behaviour.
