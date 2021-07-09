Studies for a future Web after HTML &amp; CSS

---
# Focus

These studies are focused on an alternative to the HTML + CSS. Also, the JS integration, I mean the DOM, the JS part that integrates with HTML and CSS.

What I had in mind initially was to have some sort of markup language that would, at the same time, feature content + layout + basic logic. To achieve the goal of an accessible Internet to whoever wants to create content, a slightly beefed Markdown superset would allow people to publish things on the Web. The most important thing to be added in this superset in my opinion is the ability to write trees of content and styles. However, this could even be an abstraction layer on top of a deeper foundation, powerful and flexible, aimed at the professional developers of today.

This foundation could be an extension for an existing language (think about a language that is used now for WebAssembly, like Rust). We would have then a non-markup file as an entry point for the application. In this file, that would be interpreted by lines or chunks (for speed and progressiveness) a tree object notation in JSON, YAML or even XML-like format (resembling a "pseudo-HTML") would be exposed to represent the hierarchical structure of the page.

Elements could be grouped in variables (if needed) and added to points of insertion (known in current HTML as "slots") either with instructions on the markup itself or programatically. This could be important to create dynamic pages without the need for coding. Also, to have performant reactivity without the need for frameworks, it could be desirable to only insert content to the slots and nowhere else, but this is likely subject to a deeper discussion.

Let's dive into the requisites for this proposed add-on language, or specifically for the JSON/YAML tree markup.


# Content (or the current HTML responsibilities)

## Requisites: meta and other non-content (old HEAD stuff)

- versioning(+)
- page title
- favicons & app icons (merged with meta info?)
- meta info: preloads and other headers that may impact the page or app

About style definitions (we'll mind the style later) global style definitions would be done as variables to be used wherever desired, instead of generic tag/class/selector definitions, so there would be no need to specify them in a given place. Think of this a bit how one would specify global variables and mixins on SASS for example.

P.S.: We don't need meta info for redirections, as they should be defined on the logic.

## Requisites: static content and media (old BODY stuff in general)

**HTML tags were semantic to help with SEO and accesibility**. SEO is a non-issue now, because Google (the reason why people do SEO) already has enough intelligence to infer things without caring too much about the semanticity of tags. Accessibility is something developers do wrong because it is not intuitive. In my view, instead of driving developers towards unrealistic goals that would end up with a 95% uncompliant Web it would be much better to work on the tools used for accessibility to improve their intelligence so they can infer more information from the set they are bound to have.

This above means that we don't really need semantic HTML tags. For example, we don't need a header tag when we can infer that some text is bigger than the rest based on the font size. Of course we could have a header element as a shortcut, but that's another story. For another example, it is absolutely clear where navigation menus are on a page if you look at a normal code, even without tags such `<nav>`. Even if there are more than one navigation on a page, to infer which is the main one should not be a hard job either.

Now, many pages are just collections of `<div>`s and `<span>`s anyway, and both Google and accessibility tools have to take into account that someone may be using the semantic tags wrong, as they are too many and too open for interpretation.

Based on that, my vision is to have generic elements that would be rough equivalents of `<div>` and `<span>`, supporting encapsulated layout definitions, marked interactions with or without logic (for simple actions like links) and optional meta information attached. Those elements could be marked for accessibility in a very simple and expressive way through meta info, saying for example that the content in that element is "important" or the "main content" of the page. Those elements would be:
- Box ("block" elements, that interrupt the flow of text)
- Text ("inline" elements, that do not interrupt the flow of text)

Also, the elements themselves can have all kinds of meta information that the developers judge to be relevant. A footer "box" can have a meta saying "this is a footer" or simply "footer" and the tools that interpret the markup can do whatever they want with that info. One of my biggest points here, if you didn't notice until now, is to say that we are being given the gift of expressiveness by AI and this is an attempt to reflect that. Less cognitive load and more ideas flowing.

We have to take into account special functionalities of the browser and layout elements with special elements:
- Image (with support to multiple sources)
- Video (with or without built-in UIs for control)
- Audio (with or without built-in UIs for control)
- List (highly flexible)
- Grid (a very flexible one that could also be used to generate tables, eliminating the need for tables as elements)

All tags from HTML that serve as content can be replaced with much less overhead by the generic elements presented or the special ones above.

Initially, my though was to have support for a Markdown superset, to make it easier for non-technical people to publish things on the Web. Such a support would demand that this new markup supported also tables and horizontal rules, as well as some less flexible arrangement for links and headers.

## Requisites: forms

Currently in HTML, forms are those clumsy elements, hard-to-style and full of quirks with confuse nomenclature and obvious shortcomings. The goal here would be to have elements as generic and flexible as possible (especially on the visual side).

- TextInput: as the name implies, an input just for text. This text can be represented in many different forms: number, phone, url, password, multiline plain text or single line plain text. Single line formats may benefit of a list of built-in suggestions
- Picker: the one known as `select` in HTML, but extended to also feature some things that were on `input` for whetever reason. Possible types: text, color, free templates
- File: more customisable than the current HTML implementation
- Toggle: the one known as `checkbox` but visually flexible
- ToggleGroup: not settled on this name -- the old `radio`, a group of elements in which one of them is selected
- DatePicker: intentionally separated from the picker, with different granularity: year, month, day, date and time, time only and week
- RangeSlider: also fully customisable
- Hidden: I went back and forth on this one, but we should not bring limitations to the way a form could be explored

Validation is a divisive subject. Some people love HTML native validation and some people just bypass it completely, either because they find it unreliable, or because they don't like the implementation. I don't think the implementation is the best but I don't think it is that bad. Regardless, an easier implementation would be a nice to have. Something pretty straight-forward like having an opt-out flag, a custom validator per element (that could be shared, of course) and a custom message for failed validation (for either built-in validations or custom ones). Also, because it is a common practice, maybe it would be nice to have a positive validation flag (to indicate when the field is non-pristine and valid).

About having a Form element and a submit button: ultimately, it could be inferred when we have a form on a page and when we have a button that is meant to submit the form info. To ease the programming process though a Form element is a good choice, to have a streamlined native validation and also to provide all values from a form in a grouped way. Buttons are also important, but instead of having an element just for that, a special property flag for form submission can be put on a generic element acting as button.

## Requisites: special elements

Canvas is something that doesn't make sense in the context of content. A "box" would suffice as an element to serve as a placeholder, and all the logic is completely independent from the markup content, so no need to have an element for this.

Iframes bring very little value and too much of a security risk to be deemed necessary in the context of a new Web. Portals (basically non-interactive iframes) can be useful and can be incorporated into a new markup, although their use is much more restricted and, thus, low priority.

SVG is a nice resolution-indepent format of images and it is important that it can be used as images. Also, it would be ideal that, even as images, their inner elements are made accessible. There would be a cost in performance to this though, as it would differ from the main markup.

# Style

TBD

# Interaction

TBD