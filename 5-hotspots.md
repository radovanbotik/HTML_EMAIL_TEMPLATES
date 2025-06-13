## HOTSPOT in Interactive Emails

A **progressive enhancement** technique used in HTML emails to overlay clickable “hotspots” on top of a static image. When the recipient taps or clicks a hotspot, hidden content (text, images, or labels) is revealed in place, providing a richer experience in supporting email clients while falling back gracefully in those that lack advanced CSS support.

**Resources:**

* [How to Create Interactive Hotspots in Email (Email on Acid)](https://www.emailonacid.com/blog/article/email-development/how-to-create-interactive-hotspots-in-email/)
* [Live Demo on CodePen](https://codepen.io/emailjay/pen/GzPNgX)

---

### Client Support Overview

Different email clients offer varying levels of CSS and HTML support. Below is a summary of how common clients handle the techniques used for interactive hotspots:

| Feature / Client             | Apple Mail<br />(WebKit) | Thunderbird<br />(Gecko) |        Outlook<br />(Windows)       | Gmail<br />(Web & App) |      Yahoo Mail     |     Samsung Mail    |
| ---------------------------- | :----------------------: | :----------------------: | :---------------------------------: | :--------------------: | :-----------------: | :-----------------: |
| CSS `:checked` pseudo-class  | ✅ ✅ supports form states | ✅ ✅ supports form states |      ❌ ❌ no form interactivity      |     ❌ stripping CSS    | ❌ limited CSS hacks | ❌ limited CSS hacks |
| Conditional Comments         |            n/a           |            n/a           | ✅ respects `<!--[if !mso]><!-- -->` |       ❌ stripped       |      ❌ stripped     |      ❌ stripped     |
| Media Query Hacks            |             ✅            |             ✅            |                  ❌                  |            ❌           |          ❌          |          ❌          |
| Progressive Enhancement Path |        Interactive       |        Interactive       |               Fallback              |        Fallback        |       Fallback      |       Fallback      |

> **Note:** Always provide a static fallback for unsupported clients to avoid broken layouts or missing content.

---

## 1. Core Technique: CSS Checkbox Toggle

At the heart of hotspots is the use of a **hidden checkbox** as a toggle, combined with CSS selectors to show or hide content. This pattern requires no JavaScript and relies purely on HTML and CSS.

1. **Hidden input:** Place an `<input type="checkbox">` element before your main interactive block. Apply inline styles (or the `hidden` attribute) to ensure it is not visible to the user.
2. **Clickable label:** Use a `<label for="checkbox-id">` element styled as a button or marker. When the label is clicked or tapped, it toggles the associated checkbox state.
3. **CSS sibling selector:** Leverage the `:checked` pseudo-class in combination with the **general sibling combinator** `~` to target and reveal sibling elements. Only siblings that come *after* the checkbox in the DOM can be targeted.

```html
<style>
  /* Hide the extra details by default */
  .details { display: none; }

  /* When #toggle is checked, reveal the .details sibling */
  #toggle:checked ~ .details { display: block; }
</style>

<input type="checkbox" id="toggle" hidden>
<label for="toggle">Show details</label>
<div class="details">Here are some extra details revealed on click.</div>
```

> **Why this works:**
>
> * The `:checked` pseudo-class applies only when the input is checked.
> * The `~` combinator selects any matching sibling that follows the checkbox in the HTML order.
> * No JavaScript is needed, making it safe for email environments that strip scripts.

---

## 2. Detecting Email-Client Capabilities

Emails render under a wide variety of engines—WebKit, Gecko, or Microsoft’s Word engine—each with distinct CSS support. We use a combination of **media-query hacks** and **conditional comments** to detect which path to show.

```css
/* 2.1 WebKit & Gecko detection: enables interactive path */
@media only screen and (-webkit-min-device-pixel-ratio: 0), (min--moz-device-pixel-ratio: 0) {
  /* Hide static fallback when supported */
  #webkitnocheck:checked ~ .fallback { display: none !important; }

  /* Show interactive module */
  #webkitnocheck:checked ~ .interactive { display: block !important; max-height: none !important; }
}

/* 2.2 Yahoo Mail: force static fallback */
@media screen yahoo {
  .fallback    { display: block !important; max-height: none; }
  .interactive { display: none !important; }
}

/* 2.3 Samsung Mail (Android app): force static fallback */
@media screen and (-webkit-min-device-pixel-ratio: 0) {
  body.MsgBody .fallback      { display: block !important; }
  body.MsgBody .interactive   { display: none !important; }
  /* Remove any Samsung-specific elements if needed */
  #MessageViewBody .samsung    { display: none !important; }
}

/* 2.4 Gmail hack: strips advanced CSS */
@media screen and (max-width: 9999px) {
  u + .gmailbody .fallback    { display: block !important; max-height: none; }
  u + .gmailbody .interactive { display: none !important; }
}
```

> **Key points:**
>
> * The `(-webkit-min-device-pixel-ratio:0)` and `(min--moz-device-pixel-ratio:0)` hacks target WebKit- and Gecko-based engines.
> * Yahoo and Gmail aggressively strip or ignore form and pseudo-class CSS in emails.
> * Conditional comments (`<!--[if !mso]><!-- -->`) let us exclude Outlook for Windows (which uses Word to render) from the interactive path.

---

## 3. Building the Interactive Module

### 3.1 Module Skeleton

Wrap your interactive and fallback sections within a presentation table for consistent layout in emails:

```html
<table role="presentation" width="100%" border="0" cellpadding="0" cellspacing="0">
  <tr><td align="center">

    <!--[if !mso]><!-- -->
    <input type="checkbox" id="webkitnocheck" name="webkit" checked hidden>
    <!--<![endif]-->

    <div class="fallback">
      <!-- Static image or simple content for unsupported clients -->
      <img src="images/leaf-fallback.png" alt="Static view" width="640" class="w100pc">
    </div>

    <div class="interactive samsung">
      <!-- Hotspot inputs, labels, and content go here -->
    </div>

  </td></tr>
</table>
```

* The `<!--[if !mso]><!-- -->` conditional comment ensures Outlook Windows ignores the hidden checkbox.
* By default, the `.fallback` div is visible; the `.interactive` div is hidden until we detect support.

### 3.2 Responsive Container Styling

```css
.area {
  position: relative;
  overflow: hidden;
  /* Use a background-image container so we can layer hotspots */
  background-image: url('images/leaf-bg.png');
  background-size: contain;
  background-repeat: no-repeat;

  /* Fill viewport width but cap at design size */
  width: 100vw;
  height: 100vw;   /* Keeps a square aspect ratio; adjust for other dimensions */
  max-width: 640px;
  max-height: 640px;
}
```

* Ensures that hotspots and labels always align correctly over the underlying image, regardless of client width.

---

## 4. Hotspots: Buttons, Labels, and Content

### 4.1 Defining Hotspot Buttons

Each hotspot is a visually styled `<label>` tied to a hidden `<input type="checkbox">`. The label’s `for` attribute links it to the input’s `id`, so clicking the label toggles the checkbox.

```html
<!-- Hidden form inputs for each hotspot -->
<input type="checkbox" id="leaf1" hidden>
<input type="checkbox" id="leaf2" hidden>
<input type="checkbox" id="leaf3" hidden>

<!-- Clickable labels, styled as hotspot markers -->
<label for="leaf1" class="leaf1"></label>
<label for="leaf2" class="leaf2"></label>
<label for="leaf3" class="leaf3"></label>
```

### 4.2 Button Styling & Positioning (Desktop)

```css
.leaf1, .leaf2, .leaf3 {
  position: absolute;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  box-shadow: 0 0 5px rgba(0,0,0,0.3);
  background-image: url('images/hotspot.png');
  background-size: cover;
  cursor: pointer;
  animation: pulse 2s ease infinite;
}

.leaf1 { left: 450px; top: 152px; }
.leaf2 { left: 140px; top: 230px; }
.leaf3 { left: 344px; top: 410px; }
```

* Buttons are absolutely positioned relative to the `.area` container.
* Pixel-perfect placement often requires manual trial-and-error against the background.

### 4.3 Pulse Animation to Attract Clicks

```css
@keyframes pulse {
  0%   { opacity: 0.7; transform: scale(1.0); }
  50%  { opacity: 1.0; transform: scale(1.2); }
  100% { opacity: 0.7; transform: scale(1.0); }
}
```

* Subtle animations help users notice that an element is interactive.

### 4.4 Revealing the Hotspot Content

Each hotspot’s checked state reveals its corresponding content `<div>` using a selector that chains the `:checked` and general sibling combinator:

```css
/* Base state: hide all content panels */
.area > div[id$="-content"] {
  display: none;
  position: absolute;
  width: 200px;
  height: 40px;
}

/* When leaf1 is checked, reveal its panel */
#leaf1:checked ~ .area #leaf1-content {
  display: block;
  z-index: 10;
  background-image: url('images/lamina.png');
  background-size: contain;
}
#leaf2:checked ~ .area #leaf2-content { /* similar */ }
#leaf3:checked ~ .area #leaf3-content { /* similar */ }

/* Panel positions (desktop) */
#leaf1-content { left: 375px; top: 170px; }
#leaf2-content { left:  70px; top: 248px; }
#leaf3-content { left: 270px; top: 425px; }
```

> **Why `#leaf1:checked ~ .area #leaf1-content`?**
>
> * `#leaf1:checked` ensures the rule only applies when the checkbox is active.
> * `~ .area` finds the shared container.
> * `#leaf1-content` targets the specific panel inside `.area`.

---

## 5. Mobile-First Adaptations

Small screens require reflowing absolute positions. Using viewport-relative units (`vw`, `vh`), we adapt both buttons and content for touch-friendly areas:

```css
@media screen and (max-width: 640px) {
  .w100pc { width: 100% !important; height: auto !important; }

  /* Hotspot buttons repositioned using vw units */
  .leaf1 { left: 65vw !important; top: 21vw !important; }
  .leaf2 { left: 20vw !important; top: 37vw !important; }
  .leaf3 { left: 45vw !important; top: 66vw !important; }

  /* Content panels sized and positioned responsively */
  #leaf1-content { left: 60vw; top: 23vw; width: 24vw; height: 5vw; }
  #leaf2-content { left: 15vw; top: 39vw; width: 21vw; height: 4vw; }
  #leaf3-content { left: 40vw; top: 68vw; width: 21vw; height: 4vw; }
}
```

* Viewport units ensure consistent relative positioning regardless of device pixel density or orientation.

---

## 6. Graceful Fallback Strategy

1. **Order matters:** Place the `.fallback` markup before the interactive module. If the email is truncated (e.g., Gmail’s 102KB limit), the fallback still displays.
2. **Minimal markup:** The fallback can be a single static image or simplified HTML to ensure rapid load and consistent appearance.
3. **Class toggling via CSS hacks:** Unsupported clients (Gmail, Yahoo, Samsung, Outlook) will never see the interactive section because of media-query rules and conditional comments.

```html
<div class="fallback">
  <img src="images/leaf-fallback.png" alt="Static fallback view" width="640" class="w100pc">
</div>
```

---

## 7. Putting It All Together

Wrap everything within a presentation table to preserve layouts across email clients:

```html
<table role="presentation" width="640" border="0" align="center" cellpadding="0" cellspacing="0" class="w100pc">
  <tr><td align="center" valign="top">

    <!--[if !mso]><!-- -->
    <input type="checkbox" id="webkitnocheck" name="webkit" checked hidden>
    <!--<![endif]-->

    <div class="fallback">…</div>
    <div class="interactive">…</div>

  </td></tr>
</table>
```

Ensure your CSS is inlined or in the `<head>` section, as required by your email-sending platform.

---

## 8. Summary of Best Practices

* **Progressive enhancement:** Introduce interactivity only for capable clients; never degrade core content.
* **Clear fallback:** Always guarantee a functional static version.
* **Modular structure:** Keep CSS grouped by purpose (detection, layout, buttons, content, mobile) for maintainability.
* **Testing rigorously:** Use tools like Litmus or Email on Acid to preview across clients and catch edge cases.
* **Accessibility:** Provide meaningful `alt` text for fallback images, and ensure label text conveys purpose if images fail.

