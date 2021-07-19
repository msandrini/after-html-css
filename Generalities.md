# Observations and general details

## Initial intentions

I had an alternative goal in mind: to come up with some sort of markup language that would, at the same time, feature content + layout + basic logic. Mainly, I was aiming to achieve the goal of an accessible Internet to whoever wants to create content.

The reason why I gave up is that I think it is too big of a challenge now to achieve that. It would be much better to pursue a solution that replaces the HTML + CSS duo for something simpler and more adapted to 2021.

# Requisites

## Headers from HTML

- versioning
- page title
- favicons & app icons (merged with meta info?)
- meta info: preloads and other headers that may impact the page or app
- style definitions file link

- No meta redirections

## Static content and media (old HTML BODY stuff in general)

- No semantics (read the first source article above)
- Generic layout elements (maybe with selection inline/block, maybe not)
- Special functionalities:
  - Image (with support to multiple sources)
  - Video (with or without built-in UIs for control)
  - Audio (with or without built-in UIs for control)

On elements:
- meta data
- style data
- other attributes according to functionality (TBD)

## Forms

- text input: as the name implies, an input just for text. This text can be represented in many different forms: number, phone, url, password, multiline plain text or single line plain text. Single line formats may benefit of a list of built-in suggestions
- picker: the one known as `select` in HTML, but extended to also feature some things that were on `input` for whetever reason. Possible types: text, color, free templates (slot)
- file: more visually customisable than the current HTML implementation due to slots for the inner text field (that would be optional) and the inner button. Also there could be a slot for an immediate download progress indicator, but maybe that would be unnecessary as a native implementation
- toggle: the one known as `checkbox` but visually flexible, with slots for both states (true/false)
- radio or toggle group: the old `radio`, a group of elements in which one of them is selected. Slots for both states (selected/unselected) and for each of the elements of the group when needed
- date picker: intentionally separated from the picker, with different granularity: year, month, day, date and time, time only and week. Slots for the mini-calendar and the time picker
- range slider: also fully customisable, with slots for the slider body and the slider "dot". Maybe also for a reference text on the element, but this is questionable.

- hidden: it should be there ONLY if the old form mode (send data to address) is to be supported.
- form: ultimately not necessary in all cases, but preferable to have a streamlined native validation and also to provide all values from a form serialised.
- Custom slots for validation messages
- Flag for submit "button"

## Special elements

- No canvas
- No iframes, only portals
- Support for SVG on images (styleable images somehow?)

## Style injection

- Style file with native mixins/functions/variables (only references to be used later, no selectors)
- No pseudo-elements (slots instead)
- Element states (equivalent to pseudo-classes)
  - interaction with the pointer (mouse/finger/etc.): CSS pseudo-classes for this (:hover, :active) are now somewhat limited, due to them being from the first iteration of CSS. Instead, one state related to the pointer could be exposed, carrying usable properties like the mouse coordinates when hovering, for example.
  - whether the element is empty of content, or not
  - whether the element is the first, or last, or only, or even/odd, or N-th on a list of elements or even inside a generic element.
  - whether the element has got focus or not, for forms or elements that have pointer interaction actions.
  - for form elements:
    - whether the element is valid
    - whether the element is pristine (not filled yet)
    - whether the element is required
    - whether the element is disabled
    - whether the element is read-only
  - content selection
  - first letter and first sentence (maybe first word also?)
  - svg image children elements?
- Media queries
- @Supports
- @color, @font, @keyframes, @page
- Layouts using current CSS rules

- Challenge: to take ANY state that can be inferred by JS now and assign a style to it.

---

[Go back](README.md)

