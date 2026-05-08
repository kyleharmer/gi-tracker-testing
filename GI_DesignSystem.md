# General Inspection — App Design System
*Reference for building new web apps with consistent GI visual identity*

Paste this document into a new Claude chat and say:
**"Use the GI Design System below as the visual reference for this app. Match the header, colors, fonts, and components exactly."**

---

## Brand Identity

**Company:** General Inspection LLC  
**Brand voice:** Professional, clean, data-forward. Not corporate-stiff — functional and sharp.  
**Primary color:** Deep GI green (`#2a5c14`) — used for header, active states, primary actions  
**Accent:** Lime/yellow-green (`#4a8f28`, `#3a7020`) — used in gradients  
**Logo:** GII orbital mark — dark background version uses `mix-blend-mode: screen; opacity: 0.85` on dark green header

---

## Typography

### Font Stack
```css
/* Body / UI text */
font-family: 'Barlow', 'Segoe UI', system-ui, -apple-system, sans-serif;

/* Headers / labels / tab names / condensed display */
font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
```

### Google Fonts Import (async, non-blocking)
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Barlow:wght@400;500;600;700&family=Barlow+Condensed:wght@500;600;700&display=swap"
      rel="stylesheet" media="print" onload="this.media='all'">
<noscript>
  <link href="https://fonts.googleapis.com/css2?family=Barlow:wght@400;500;600;700&family=Barlow+Condensed:wght@500;600;700&display=swap" rel="stylesheet">
</noscript>
```

### Font Smoothing (global — fixes Windows rendering)
```css
* {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
}
```

### Type Scale
| Use | Family | Size | Weight |
|-----|--------|------|--------|
| Body / table content | Barlow | 13–14px | 400 |
| Form labels | Barlow | 10px | 700, uppercase, 0.08em spacing |
| Tab names | Barlow Condensed | 12px | 700, uppercase, 0.06em spacing |
| Section headers | Barlow Condensed | 14–18px | 700, uppercase |
| App title in header | Barlow Condensed | 21px | 700, uppercase, 0.05em spacing |
| Stat numbers | Barlow Condensed | 24px | 700 |
| Status / priority pills | Barlow | 11px | 600 |
| Small metadata | Barlow | 10–11px | 400–700 |

---

## Color Palette

### CSS Custom Properties (paste into `:root`)
```css
:root {
  /* GI Green — primary brand */
  --gig:       #2a5c14;   /* primary green — buttons, active states, headers */
  --gig-mid:   #3a7d1e;   /* mid green — hover states */
  --gig-light: #e8f5e0;   /* light green tint — badges, chips */
  --gig-xlight:#f4faf0;   /* very light green — backgrounds */

  /* App surfaces */
  --bg:    #f2f4f0;   /* page background — slightly green-tinted grey */
  --surf:  #ffffff;   /* card / modal / input backgrounds */
  --surf2: #f8faf6;   /* secondary surface — table alternates, config panels */

  /* Borders */
  --bdr:  #dde0d8;   /* standard border */
  --bdr2: #b8bdb0;   /* stronger border */

  /* Text */
  --tx:  #1a1e17;    /* primary text — near black with green tint */
  --txm: #6b7263;    /* muted text — labels, metadata */
  --txl: #9aa392;    /* light text — placeholders, disabled */

  /* Semantic — danger */
  --red:     #b93328;
  --red-bg:  #fdf0ef;
  --red-bdr: #f5c0bb;

  /* Semantic — warning */
  --ylw:     #8a6200;
  --ylw-bg:  #fffbec;
  --ylw-bdr: #edd98a;

  /* Semantic — success */
  --grn:     #1a6b2a;
  --grn-bg:  #edf7f0;
  --grn-bdr: #a8d9b4;

  /* Semantic — info / action */
  --blue:    #1a4fbf;
  --blue-bg: #e8f1ff;

  /* Border radius */
  --r:  8px;    /* standard — inputs, cards, buttons */
  --rl: 12px;   /* large — modals, panels */
}
```

---

## Header

The header is the most distinctive GI element. Always sticky, always green gradient.

```css
.hdr {
  background: linear-gradient(135deg, #2a5018 0%, #3a7020 45%, #4a8f28 100%);
  position: sticky;
  top: 0;
  z-index: 200;
  box-shadow: 0 2px 8px rgba(0,0,0,.3);
}
.hdr-inner {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 8px 20px;
}
.hdr-logo {
  height: 50px;
  width: auto;
  /* If logo has dark/black background, add: */
  /* mix-blend-mode: screen; opacity: 0.85; */
}
.hdr-title {
  font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
  font-size: 21px;
  font-weight: 700;
  color: #fff;
  letter-spacing: .05em;
  text-transform: uppercase;
  opacity: .9;
  flex: 1;
}
```

### Header Buttons
```css
/* Ghost button — default header action */
.hbtn {
  background: rgba(255,255,255,.15);
  border: 1px solid rgba(255,255,255,.3);
  color: #fff;
  padding: 6px 14px;
  border-radius: var(--r);
  font-family: 'Barlow', 'Segoe UI', system-ui, sans-serif;
  font-size: 12px;
  font-weight: 600;
  cursor: pointer;
  white-space: nowrap;
}
.hbtn:hover { background: rgba(255,255,255,.25); }

/* Primary header button — stands out from ghost buttons */
.hbtn.primary {
  background: rgba(255,255,255,.9);
  color: var(--gig);
}
.hbtn.primary:hover { background: #fff; }
```

### HTML Structure
```html
<div class="hdr">
  <div class="hdr-inner">
    <img src="LOGO_URL" alt="General Inspection" class="hdr-logo">
    <div class="hdr-title">App Name</div>
    <div class="hdr-right" style="display:flex;align-items:center;gap:8px">
      <button class="hbtn">Secondary Action</button>
      <button class="hbtn primary">Primary Action</button>
    </div>
  </div>
</div>
```

---

## Tab Bar

Sits directly below the header. Dark green, tabs lift up when active.

```css
.tabs-bar {
  background: #1a3a0a;
  padding: 0 20px;
  display: flex;
  gap: 2px;
  align-items: flex-end;
  overflow-x: auto;
  scrollbar-width: none;
}
.tab-btn {
  font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
  font-size: 12px;
  font-weight: 700;
  letter-spacing: .06em;
  text-transform: uppercase;
  padding: 8px 16px;
  border: none;
  background: transparent;
  color: rgba(255,255,255,.5);
  cursor: pointer;
  border-radius: 5px 5px 0 0;
  transition: all .15s;
  white-space: nowrap;
}
.tab-btn:hover { background: rgba(255,255,255,.1); color: rgba(255,255,255,.8); }
.tab-btn.active { background: var(--bg); color: var(--gig); }
```

---

## Buttons

```css
/* Primary action — green */
.btn-save {
  background: var(--gig);
  border: 1px solid var(--gig);
  color: #fff;
  padding: 7px 22px;
  border-radius: var(--r);
  font-family: 'Barlow', 'Segoe UI', system-ui, sans-serif;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
}
.btn-save:hover { background: var(--gig-mid); }

/* Secondary / cancel */
.btn-cancel {
  background: none;
  border: 1px solid var(--bdr);
  color: var(--txm);
  padding: 7px 18px;
  border-radius: var(--r);
  font-family: 'Barlow', 'Segoe UI', system-ui, sans-serif;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}
.btn-cancel:hover { background: var(--surf2); }

/* Outline action button — e.g. Export, Configure */
.lbtn {
  background: var(--surf);
  border: 1px solid var(--bdr);
  color: var(--txm);
  padding: 5px 12px;
  border-radius: var(--r);
  font-size: 12px;
  font-weight: 600;
  cursor: pointer;
}
.lbtn:hover { border-color: var(--bdr2); color: var(--tx); }

/* Danger variant */
.lbtn.danger { color: var(--red); border-color: var(--red-bdr); }
```

---

## Forms

```css
.form-group { display: flex; flex-direction: column; gap: 5px; }
.form-group.full { grid-column: 1 / -1; }

.form-label {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: .08em;
  color: var(--txm);
}

/* Shared input base */
.form-input, .form-select, .form-textarea, .search-inp, .flt-sel {
  font-family: 'Barlow', 'Segoe UI', system-ui, sans-serif;
  font-size: 13px;
  border: 1px solid var(--bdr);
  border-radius: var(--r);
  padding: 7px 9px;
  background: var(--surf);
  color: var(--tx);
  width: 100%;
}
.form-input:focus, .form-textarea:focus, .form-select:focus {
  outline: none;
  border-color: var(--gig);
  box-shadow: 0 0 0 3px rgba(42,92,20,.12);
}
.form-textarea { resize: vertical; min-height: 70px; }
```

---

## Cards / Stat Cards

```css
.stat-card {
  background: var(--surf);
  border: 1px solid var(--bdr);
  border-radius: var(--r);
  padding: 10px 14px;
  box-shadow: 0 1px 3px rgba(0,0,0,.07);
}
/* Variants */
.stat-card.danger { border-left: 3px solid var(--red); }
.stat-card.warning { border-left: 3px solid var(--ylw); }
.stat-card.success { border-left: 3px solid var(--grn); }

.stat-val {
  font-size: 24px;
  font-weight: 700;
  font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
  color: var(--tx);
}
.stat-val.danger { color: var(--red); }
.stat-val.warning { color: var(--ylw); }
.stat-val.success { color: var(--grn); }

.stat-lbl {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: .08em;
  color: var(--txm);
  margin-bottom: 3px;
}
```

---

## Modals

```css
.modal-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,.4);
  z-index: 500;
  align-items: center;
  justify-content: center;
  padding: 20px;
}
.modal-overlay.open { display: flex; }

.modal {
  background: var(--surf);
  border-radius: var(--rl);
  padding: 24px;
  width: 100%;
  max-width: 620px;
  box-shadow: 0 8px 32px rgba(0,0,0,.15);
  margin: auto;
}
.modal-title {
  font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
  font-size: 18px;
  font-weight: 700;
  color: var(--gig);
  margin-bottom: 18px;
  text-transform: uppercase;
  letter-spacing: .05em;
}
.modal-acts {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
  margin-top: 20px;
  padding-top: 16px;
  border-top: 1px solid var(--bdr);
}
```

---

## Status Pills

Used to display task/record status inline.

```css
.status-pill {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
}
.s-ongoing  { background: #e8f1ff; color: #1a4fbf; }
.s-planned  { background: var(--ylw-bg); color: var(--ylw); }
.s-complete { background: var(--grn-bg); color: var(--grn); }
.s-hold     { background: #f5f0ff; color: #6b3db5; }
.s-delayed  { background: var(--red-bg); color: var(--red); }
.s-other    { background: var(--surf2); color: var(--txm); }
```

---

## Priority Badges

```css
.pri-high {
  display: inline-flex; align-items: center; gap: 3px;
  font-size: 11px; font-weight: 700;
  color: #b93328; background: #fdf0ef; border: 1px solid #f5c0bb;
  padding: 2px 7px; border-radius: 4px;
}
.pri-med {
  display: inline-flex; align-items: center; gap: 3px;
  font-size: 11px; font-weight: 700;
  color: #8a6200; background: #fffbec; border: 1px solid #edd98a;
  padding: 2px 7px; border-radius: 4px;
}
.pri-low {
  display: inline-flex; align-items: center; gap: 3px;
  font-size: 11px; font-weight: 700;
  color: var(--txm); background: #f0f0f0; border: 1px solid var(--bdr);
  padding: 2px 7px; border-radius: 4px;
}
```

---

## Due Date Badges

```css
.due-badge {
  display: inline-flex; align-items: center; gap: 4px;
  padding: 2px 9px; border-radius: 20px;
  font-size: 11px; font-weight: 700; white-space: nowrap;
}
.due-overdue { background: var(--red-bg); color: var(--red); border: 1px solid var(--red-bdr); }
.due-soon    { background: var(--ylw-bg); color: var(--ylw); border: 1px solid var(--ylw-bdr); }
.due-ok      { background: var(--grn-bg); color: var(--grn); border: 1px solid var(--grn-bdr); }
.due-na      { background: var(--surf2);  color: var(--txl); border: 1px solid var(--bdr); }
```

---

## People Chips

```css
/* Owner — green */
.owner-chip {
  display: inline-block;
  background: var(--gig-light);
  color: var(--gig);
  font-size: 11px;
  font-weight: 700;
  padding: 2px 7px;
  border-radius: 20px;
}

/* Supporting — blue */
.support-chip {
  display: inline-block;
  background: var(--blue-bg);
  color: var(--blue);
  font-size: 11px;
  font-weight: 600;
  padding: 2px 7px;
  border-radius: 20px;
}

/* PM / category badge — green outlined */
.pm-badge {
  display: inline-block;
  background: var(--gig-light);
  color: var(--gig);
  font-size: 10px;
  font-weight: 700;
  padding: 2px 8px;
  border-radius: 4px;
}
```

---

## Toast Notifications

```css
.toast {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: var(--gig);
  color: #fff;
  padding: 10px 18px;
  border-radius: var(--r);
  font-size: 13px;
  font-weight: 600;
  box-shadow: 0 4px 14px rgba(0,0,0,.2);
  z-index: 999;
  transform: translateY(70px);
  opacity: 0;
  transition: all .25s;
}
.toast.show { transform: translateY(0); opacity: 1; }
```

```javascript
// Usage
function showToast(msg) {
  var el = document.getElementById('toast');
  el.textContent = msg;
  el.classList.add('show');
  setTimeout(function() { el.classList.remove('show'); }, 2500);
}
```

---

## Tables

```css
.data-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}
.data-table th {
  background: var(--surf2);
  padding: 6px 10px;
  text-align: left;
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: .07em;
  color: var(--txm);
  border-bottom: 2px solid var(--bdr);
  white-space: nowrap;
}
.data-table td {
  padding: 8px 10px;
  border-bottom: 1px solid var(--bdr);
  vertical-align: top;
}
.data-table tbody tr:hover { background: var(--gig-xlight); }
```

---

## Section Headers (within content area)

```css
.section-hdr {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 9px 14px;
  background: var(--gig);
  color: #fff;
  border-radius: var(--r) var(--r) 0 0;
  font-family: 'Barlow Condensed', 'Segoe UI Semibold', 'Segoe UI', system-ui, sans-serif;
  font-size: 13px;
  font-weight: 700;
  letter-spacing: .04em;
  text-transform: uppercase;
}
```

---

## Page Layout

```css
body {
  font-family: 'Barlow', 'Segoe UI', system-ui, -apple-system, sans-serif;
  background: var(--bg);
  color: var(--tx);
  min-height: 100vh;
  font-size: 14px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.main {
  padding: 20px;
  max-width: 1400px;
  margin: 0 auto;
}
```

---

## Minimal Full HTML Shell

Use this as the starting point for any new GI app:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <title>General Inspection — App Name</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Barlow:wght@400;500;600;700&family=Barlow+Condensed:wght@500;600;700&display=swap"
        rel="stylesheet" media="print" onload="this.media='all'">
  <style>
    /* Paste full :root and component CSS here */
  </style>
</head>
<body>

<div class="hdr">
  <div class="hdr-inner">
    <img src="GI_LOGO" alt="General Inspection" class="hdr-logo">
    <div class="hdr-title">App Name</div>
    <div style="display:flex;align-items:center;gap:8px">
      <button class="hbtn">Action</button>
      <button class="hbtn primary">Primary</button>
    </div>
  </div>
</div>

<div class="tabs-bar" id="tabsBar">
  <!-- tabs injected here -->
</div>

<div class="main">
  <div id="appContent">
    <!-- content injected here -->
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
  // App JS here
</script>
</body>
</html>
```

---

## Design Principles

1. **Green is the brand.** Use `--gig` for primary actions, active states, and section headers. Never use a generic blue as the primary.

2. **Condensed for display, regular for body.** Barlow Condensed is for headers, tab names, stat numbers, modal titles. Barlow regular for body text, form fields, descriptions.

3. **Uppercase labels everywhere.** Form labels, table headers, stat labels, tab names — always uppercase, small size, wide letter-spacing. It's a core part of the look.

4. **Semantic color for status.** Red = problem/overdue, Yellow = warning/pending, Green = good/complete, Blue = informational/ongoing. Never use these colors for decoration.

5. **Surfaces are slightly green-tinted.** `--bg` (#f2f4f0), `--surf2` (#f8faf6) have a subtle green cast rather than pure grey. This ties the whole UI to the brand color without being heavy-handed.

6. **Rounded but not playful.** `--r: 8px` on most things, `--rl: 12px` for modals. Consistent, clean, not bubbly.

7. **Shadows are subtle.** Cards: `0 1px 3px rgba(0,0,0,.07)`. Modals: `0 8px 32px rgba(0,0,0,.15)`. Header: `0 2px 8px rgba(0,0,0,.3)`. Never loud.

8. **White text on dark green header only.** Everything else uses `--tx` / `--txm` / `--txl` on light surfaces.
