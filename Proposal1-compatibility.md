# Proposal 1: Extended, but similar to HTML (prioritise compatibility)

## Intentions

- To improve HTML header, keeping compatibility high
- On HTML body, also keep compatibility with HTML as high as possible while removing semantic tags and improving form controls
- Compatibility with existing CSS rules while moving styles away from selectors
- Expand styles towards a programmatic approach
- Implement template (slots) into "light DOM" for more customisable form elements

For the markup and the stylesheet, I created two silly names only for them to be taken as a separate thing from HTML and CSS: WOML (Web Optimised Markup Language) and WOSS (Web Optimised Style Sheets).

## Example

### Style (WOSS)
```
--baseFontSize: 1rem

@general {
    font-family: Arial, sans-serif
}

/* silly example, just to showcase if and returns */
@macro allMarginsButLeft (size = .5rem) {
    if (size) {
        return {
            margin: size size size 0
        }
    } else {
        return {}
    }
}

twoCols {
    grid: '1fr 1fr';
}
title {
    fontSize: var(--baseFontSize);
    fontWeight: bold;
    color: red;
}
subtitle {
    ...title;
    fontWeight: normal;
    fontSize: calc(@get title/fontSize - .2rem)
    ...allMarginsButLeft(1rem);
}
normalText {
    fontSize: .5rem;
}
submitButton {
    background: white;
    border: 2px black;
    padding: .1rem;
    :hover {
        background: rgba(255, 255, 0, .5);
    }
}
clearButton {
    ...submitButton;
    opacity: .8;
    :hover {
        background: gray;
    }
}
uploadButton {
    ...clearButton;
    fontSize: .2rem;
}
radioLabel {
    padding: .2rem;
    border: 1px black;
    :last-of-type {
        margin-right: 1rem;
    }
    on {
        background: red
        color: white
    }
}

```

### Content (WOML)
```
<!doctype woml="1">
<woml>
    <head>
        <title>My Web App</title>
        <icon src="assets/myfile.png" sizes="32x32" />
        <icon src="assets/myfile@2x.png" sizes="64x64" />
        <preload href="some-image.jpg" as="image" />
        <style src="./style.woss"></style>
    </head>
    <body>
        <div class="twoCols">
            <div>
                <picture alt="My Experimental Website">
                    <source srcset="logo.png 1x, logo@2x.png 2x" media="(min-width: 600px)">
                    <source srcset="logo-mobile.png">
                </picture>
            </div>
            <div>
                <div class="title" meta="this is the title!">Hello world!</div>
                <div class="subtitle">An awesome experience with Markups</div>
                <div class="normalText">
                    Now let us have...
                    <span style="{ color: rgb(255, 192, 0) }">another text!</span>
                </div>
            </div>
        </div>
        <form onclick="sendForm">
            <div class="label">E-mail:</div>
            <input type="email" name="email" required>
                <template slot="errorValidationMessage">
                    <div style="{ color: red }">⚠️ <slot name="message" /></div>
                </template>
            </input>
            <div class="label">Location:</div>
            <select name="location">
                <option value="from Town">
                    <div style="color: green">from this Town</div>
                </option>
                <option value="from out of Town">
                    <picture srcset="out-of-town.png" alt="out of town" />
                </option>
            </select>
            <div class="label">Message:</div>
            <input type="multiline" name="message">
                <template slot="errorValidationMessage">
                    <div style="{ color: red }"><slot name="message" /></div>
                </template>
            </input>
            <div class="label">Attachment:</div>
            <file name="attachment">
                <template slot="field">
                    <input style="font-size: .5rem" />
                </template>
                <template slot="button">
                    <div class="uploadButton">please upload</div>
                </template>
            </file>
            <div class="label">File type:</div>
            <radio name="fileType" value="PDF">
                <template slot="on">
                    <picture srcset="radio-on.svg" alt="on" />
                    <div class="radioLabel on"><slot /></div>
                </template>
                <template slot="off">
                    <picture srcset="radio-off.svg" alt="off" />
                    <div class="radioLabel"><slot /></div>
                </template>
                <option>PDF</option>
                <option>Word</option>
            </radio>
            <div class="twoCols">
                <div formclear class="clearButton normalText">clear</div>
                <div formsubmit class="submitButton normalText">Send!</div>
            </div>
        </form>
    </body>
</woml>
```

## Differences to HTML/CSS

### Styles

- Styles: no selectors; only declarations for "classes"
- `@general` for declarations that affect all elements
- `@macro` on styles (functions)
- Operator ... on styles (alias: `@extend`) to extend declaration including wither the contents of a macro output or another class declaration (still possible to extend class on `class` attribute on markup too)
- `@get` to inject a single rule from other declaration (accessible via dot notation)
- `solid` is default on `border[*]` rule
- Pseudo-elements such as `:hover` to be nested *inside* the rules
- Private rules: style rules nested that are only applicable when the parent is called

### Head

- Different DOCTYPE with required version
- `woml` tag, identical to `html`, supports `html` as alias
- `icon` tag to replace `<link rel=icon>` (same shape)
- `preload` tag to replace `<link rel=preload>` (same shape)
- `style` extended to be more similar to `script`, loading scripts from `src` (with `@import` still supported)

### Body

- No semantic tags, for content only `div` and `span`
- Attribute `meta` (to host free-formed information for i.e. crawlers and screen readers)
- Inline styles (via `style` attribute) can be inside curly braces `{}` to support pseudo-elements (`:hover`, for example)
- `picture` element with `alt` attribute (no `img` fallback anymore, as no `img` tag would exist) and also with `source` attributes (for only one source set)
- No `canvas`, `iframe`

### Form-specific

- `input` tag now comprising the old types `text`, `email`, `password`, `url`, `search`, `tel`, plus `multiline` (to replace `textarea`, to be extinct)
- `input` tag with a slot template for error validation message (`errorValidationMessage`)
- `select` with types: `text` (default), `color` and date/time types: `year`, `month`, `day`, `datetime`, `time`, `week`.
- Range slider in its own tag `range`, with templates for the `slider` and the `dragHandle`. Also support for `option` tags inside it (the same functionality as currently with a `datalist` on HTML)
- File input in its own tag `file`, with templates for the `field` and the `button`
- Checkbox (toggler) in its own tag `check`, with templates for the element in both `on` and `off` states
- Radio (group toggler) in its own tag `radio`, with templates for the element in both `on` and `off` states
- The `radio` is not a control for individual values anymore, now it should take values/labels from `<option>` elements inside it
- Attributes `formsubmit` and `formclear` for `div` elements to be used as submit/clear buttons

### Possible aliases

- `block` for `div`
- `inline` for `span`
- `textfield` for `input`
- `picker` for `select`

---

[Go back](README.md)
