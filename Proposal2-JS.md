# Proposal 1: JS-based, no markups

## Intentions

- To improve HTML header, keeping compatibility high
- On HTML body, also keep compatibility with HTML as high as possible while removing semantic tags and improving form controls
- Compatibility with existing CSS rules while moving styles away from selectors
- Expand styles towards a programmatic approach
- Implement template (slots) into "light DOM" for more customisable form elements

For the markup and the stylesheet, I created two silly names only for them to be taken as a separate thing from HTML and CSS: WOML (Web Optimised Markup Language) and WOSS (Web Optimised Style Sheets).

## Example

### Separate JS file only for styles

We would create an API called **`document.style`**, made to replace CSS. This API would feature helpers to allow a complete style declaration directly from JS, given the changes mentioned above.

```js
/* Destructuring: recommended to keep names short for a quicker use */

const { rem, fr, px } = document.style.units
const { rgba, border } = document.style.models
const { fontWeight, colors, borderStyle } = document.style.enums
const { raw, houdini } = document.style.compatibility

/*
Units are hard to write in JS, so redundancy is important.
I am introducing here what I call "unit methods" but we also may have
a function to weite units as in CSS (full string) and in a Houdini-like way
*/
const baseFontSize = rem(1) // also writable as raw`1rem`, or houdini({ value: 1, unit: 'rem' })

/* general style declarations */
document.style.general = {
    fontFamily: ['Arial', 'sans-serif']
}

/* silly example, just to showcase a function */
const allMarginsButLeft = (size = rem(.5)) => {
    if (size) {
        return {
            margin: [size, size, size, 0]
        }
    } else {
        return {}
    }
}

/* 
Here I'm writing one big style declaration at once, but I could just as well establish a style
variable and add the classes one by one to it. This would allow for referencing previous rules.

The important thing is: we export those styles and they serve as tags for later
*/
export const style = {
    // naming cases for classes as camelCase is just a suggestion
    twoCols: {
        grid: [fr(1), fr(1)]
    },
    title: {
        fontSize: baseFontSize,
        fontWeight: fontWeight.BOLD, // also window.style.units.fontWeight(900)
        color: colors.NAVYBLUE
    },
    subtitle: {
        fontWeight: fontWeight.NORMAL,
        fontSize: rem(baseFontSize - .2), // calculations can happen inside unit methods
        ...allMarginsButLeft(rem(1))
    },
    normalText: {
        fontSize: rem(.5)
    },
    submitButton: {
        background: colors.WHITE,
        // border is a "model", a function that has 2 or 3 arguments
        border: border(px(2), colors.BLACK), // border style defaults to SOLID
        padding: rem(1),
        // here we have what is called in CSS the pseudo-selectors
        state: {
            // we can have it for any event, in this case a mouse event
            mouseover: {
                background: rgba(255, 255, 0, .5) // rgba is also a model
            }
        }
    },
    clearButton: {
        background: colors.WHITE,
        opacity: .8
    },
    uploadButton: {
        fontSize: rem(.2)
    }
    radioLabel {
        padding: rem(.2),
        border: border(px(2), colors.BLACK, borderStyle.SOLID),
        state: {
            isLastOfType: { // special pseudo-selector, see list below
                marginRight: rem(1)
            }
        },
        isOn: { // nested rule: applicable when the parent is also declared on element
            background: colors.RED
        }
    },
    error: {
        color: colors.RED
    }
}

```

### Content JS file

This proposal relies on someting not supported by current Web apps: **entry files in JavaScript**.
That means, no dummy HTML to call a JS file anymore, as everything that would be on an "index" HTML would be on the JS entry file below.

We would create an API called **`document.content`**, made to replace HTML structures. This API would have many methods and helpers to allow for the same level of content creation HTML would allow (given the differences we want to feature) with everything running stright from JS.

```js
/* Destructuring: not required of course, but an option to consider */

const { preloadFormats, icon, preload, render } = document.content
const { rgb } = document.style.models

/* Versioning: relevant to keep the API standardised und updated */

document.content.version = 1

/* Those below are equivalent to HTML headers */

document.title = 'My Web App'
registerIcon('assets/myfile.png', { sizes: '32x32' })
registerIcon('assets/myfile@2x.png', { sizes: '64x64' })
preload('some-image.jpg', preloadFormats.IMAGE) // as expected, using the enum is optional

const { styles } = await import 'styles.js' // styles being imported

const inlineStyle = { color: rgb(255, 192, 0) }

/* I thought of a render method, but HOW?
JSX would be perfect, but it requires transpiling
Plain HTML strings is just a sub-par solution, not ideal for integrating scripts like this
JSON-like structures are a pain to write (too verbose for trees like this)
YAML or HAML depend on indentation, which looks revolting to too many people (not me though)

For now, HTML it is
*/
render(
    `<body>
        <div.twoCols>
            <div>
                <picture alt="My Experimental Website">
                    <source srcset="logo.png 1x, logo@2x.png 2x" media="(min-width: 600px)">
                    <source srcset="logo-mobile.png">
                </picture>
            </div>
            <div>
                <div.title meta="this is the title!">Hello world!</div>
                <div.subtitle>An awesome experience with Markups</div>
                <div.normalText>
                    Now let us have...
                    <span style="${inlineStyle}">another text!</span>
                </div>
            </div>
        </div>
        <form onclick="sendForm">
            <div.label>E-mail:</div>
            <input type="email" name="email" required>
                <template slot="errorValidationMessage">
                    <div.error>⚠️ <slot name="message" /></div>
                </template>
            </input>
            <div.label>Location:</div>
            <select name="location">
                <option value="from Town">
                    <div>from this Town</div>
                </option>
                <option value="from out of Town">
                    <picture srcset="out-of-town.png" alt="out of town" />
                </option>
            </select>
            <div.label>Message:</div>
            <input type="multiline" name="message">
                <template slot="errorValidationMessage">
                    <div.error><slot name="message" /></div>
                </template>
            </input>
            <div.label>Attachment:</div>
            <file name="attachment">
                <template slot="field">
                    <input />
                </template>
                <template slot="button">
                    <div.uploadButton">please upload</div>
                </template>
            </file>
            <div.label>File type:</div>
            <radio name="fileType" value="PDF">
                <template slot="on">
                    <picture srcset="radio-on.svg" alt="on" />
                    <div.radioLabel.on><slot /></div>
                </template>
                <template slot="off">
                    <picture srcset="radio-off.svg" alt="off" />
                    <div.radioLabel><slot /></div>
                </template>
                <option>PDF</option>
                <option>Word</option>
            </radio>
            <div.twoCols">
                <div.clearButton.normalText formclear>clear</div>
                <div.submitButton.normalText formsubmit>Send!</div>
            </div>
        </form>
    </body>
`)
```

## Differences from how things work today

### Web entry point

- **MAJOR POINT**: The entry point of Web applications would have to be changed to JS files
- Optional: These entry-point JS files also could behave differently to be optimised for incremental loading, with no hoisting so applications could benefit from gradual loading

### JS

- 

### Styles
- General declarations
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
