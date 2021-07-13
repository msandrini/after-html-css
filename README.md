Studies for a future Web after HTML &amp; CSS

---
# Focus

These studies are focused on an alternative to the HTML + CSS. Also, the JS integration, I mean the DOM, the JS part that integrates with HTML and CSS.

What I had in mind initially was to have some sort of markup language that would, at the same time, feature content + layout + basic logic. To achieve the goal of an accessible Internet to whoever wants to create content, a slightly beefed Markdown superset would allow people to publish things on the Web. The most important thing to be added in this superset in my opinion is the ability to write trees of content and styles. However, this could even be an abstraction layer on top of a deeper foundation, powerful and flexible, aimed at the professional developers of today.

This foundation could be an extension for an existing language (think about a language that is used now for WebAssembly, like Rust), although this is not the focus of this study. What is important is that we would have then a non-markup file as an entry point for the application. In this file, that could be interpreted by lines or chunks (for speed and progressiveness) a tree object notation in JSON, YAML or even XML-like or JSX-like format (resembling a "pseudo-HTML") would be exposed to represent the hierarchical structure of the page.

Elements could be grouped in variables (if needed) and added dynamically to points of insertion (known in current HTML as *slots*, whose name could be reused as it seems like a good name). Those slots would be filled either with instructions on the markup itself or programatically. This could be important to create dynamic pages without the need for coding. Also, to have performant reactivity without the need for frameworks, it could be perhaps desirable to only insert content to these slots and nowhere else, but this is likely subject to a deeper discussion.

Another use for the "slot-like" structure would be to have a functionality similar to what is offered by Shadow DOM structures in elements, but extended like some Vue.js components, that use slots for sub-elements, often with comprehensive defaults, avoiding the need for many current CSS pseudo-elements and bringing way more flexibility than we have today. More on that later.

The biggest point here is making something sensible out of all this constant and, in my opinion, unnecessary bridging between HTML, CSS and JS. All the expressiveness lost in this constant back-and-forth is probably one of the most significant reasons keeping web development from being more enjoyable and efficient. Also there is the very important aspect of maintainability, which is affected by this heavily especially by CSS, that was deemed "unorganisable" despite well-thought standards like BEM and sophisticate preprocessors, only because CSS itself was never designed for the kind of web applications we do now.

One of my additional points here, as you will notice, is to try reflecting an age when we're being given the gift of expressiveness by AI, especially when preparing websites and web apps for Google, which is smart enough by now. All of this to achieve less cognitive load and more ideas flowing.

Let's dive into the requisites for this proposed add-on language, or specifically for the JSON/YAML tree markup.


# Content

A.k.a. the current HTML resposibilities.

## Meta and other non-content (old HEAD stuff)

- versioning(+)
- page title
- favicons & app icons (merged with meta info?)
- meta info: preloads and other headers that may impact the page or app

About style definitions (we'll mind the style later) global style definitions would be done as variables to be used wherever desired, instead of generic tag/class/selector definitions, so there would be no need to specify them in a given place. Think of this a bit how one would specify global variables and mixins on SASS for example. Later, when discussing layouts and styles, this will be explored further.

P.S.: We don't need meta info for redirections, as this is a very legacy way of redirecting and, in any case, these redirections should be done on the logic.

## Static content and media (old BODY stuff in general)

**HTML tags were semantic to help with SEO and accesibility**. SEO is a non-issue now, because Google (the reason why people do SEO) already has enough intelligence to infer things without caring too much about the semanticity of tags. Accessibility is something developers do wrong because it is not intuitive. In my view, instead of driving developers towards unrealistic goals that would end up with a 95% uncompliant Web it would be much better to work on the tools used for accessibility to improve their intelligence so they can infer more information from the set they are bound to have.

This above means that we don't really need semantic HTML tags. For example, we don't need a header tag when we can infer that some text is bigger than the rest based on the font size. Of course we could have a header element as a shortcut, but that's another story. For another example, it is absolutely clear where navigation menus are on a page if you look at a normal code, even without tags such `<nav>`. Even if there are more than one navigation on a page, to infer which is the main one should not be a hard job either.

Now, many pages are just collections of `<div>`s and `<span>`s anyway, and both Google and accessibility tools have to take into account that someone may be using the semantic tags wrong, as they are too many and too open for interpretation.

Based on that, my vision is to have generic elements that would be rough equivalents of `<div>` and `<span>`, supporting encapsulated layout definitions, marked interactions with or without logic (in the case of simple actions like links) and optional meta information attached. Those elements could be marked for accessibility in a very simple and expressive way through meta info, saying for example that the content in that element is the "main content" of the page or that it is just presentational and even something as expressive as "should not be read by screen readers" (I know this is probably way too much to ask for screen readers, but we should really invest into them instead of forcing difficult accessibility rules that will never be followed thoroughly). Those elements would be:
- Box ("block" elements, that interrupt the flow of text)
- Text ("inline" elements, that do not interrupt the flow of text)

Also, the elements themselves can have all kinds of meta information that the developers judge to be relevant. A footer "box" can have a meta saying "this is a footer" or simply "footer" and the tools that interpret the markup can do whatever they want with that info.

We have to take into account special functionalities of the browser and layout with special elements:
- Image (with support to multiple sources)
- Video (with or without built-in UIs for control)
- Audio (with or without built-in UIs for control)
- List (highly flexible, with support to slots for the content. A bit more on it later)

All tags from HTML that serve as content can be replaced with much less overhead by the generic elements presented or the special ones above, without need to have in one's head how a given tag is adequate for a given situation (but again, with the possibility of inserting all supplementary data that one may judge as feasible to screen readers or search engines via meta information).

## Forms

Currently in HTML, forms are those clumsy elements, hard-to-style and full of quirks with confuse nomenclature and obvious shortcomings. The goal here would be to have elements as generic and flexible as possible (especially on the visual side).

One thing about most HTML form controls is that they are reproducible in other ways. To achieve a native-looking `<input type=text>` for example, one could have a box with border and background and position the text inside with the `contentEditable` flag activated (there are some tweaks for preventing text formatting also, but this is the main idea). A good implementation of the tags below would be something that is just as transparent, with the advantage of having slots that are completely editable to make the whole element flexible.

Possible slots for all elements below: label (may also be a separate element), placeholder text, validation message, marks for both the "invalid" and for the "valid" states. With slots for those, one can insert icons/emoji or whatever else to signalise the element is valid, for example, without need for logic or clumsy pseudo-elements boilerplate.

- TextInput: as the name implies, an input just for text. This text can be represented in many different forms: number, phone, url, password, multiline plain text or single line plain text. Single line formats may benefit of a list of built-in suggestions
- Picker: the one known as `select` in HTML, but extended to also feature some things that were on `input` for whetever reason. Possible types: text, color, free templates (slot)
- File: more visually customisable than the current HTML implementation due to slots for the inner text field (that would be optional) and the inner button. Also there could be a slot for an immediate download progress indicator, but maybe that would be unnecessary as a native implementation
- Toggle: the one known as `checkbox` but visually flexible, with slots for both states (true/false)
- ToggleGroup: not settled on this name -- the old `radio` (maybe it became a better name after all), a group of elements in which one of them is selected. Slots for both states (selected/unselected) and for each of the elements of the group when needed
- DatePicker: intentionally separated from the picker, with different granularity: year, month, day, date and time, time only and week. Slots for the mini-calendar and the time picker
- RangeSlider: also fully customisable, with slots for the slider body and the slider "dot". Maybe also for a reference text on the element, but this is questionable.
- Hidden: I went back and forth on this one, but from one point we should not bring limitations to the way a form could be explored for people not familiarised with logic.
- Form: ultimately not necessary in all cases, but preferable to have a streamlined native validation and also to provide all values from a form serialised.

Validation is a divisive subject. Some people love HTML native validation and some people just bypass it completely, either because they find it limited, because they want to support older browser that don't implement them or because they don't like the implementation. Regardless of whether the implementation is good enough or not, an easier and more flexible implementation would be a nice to have. Something pretty straight-forward like having an opt-out flag like today, a custom validator per element (that could be shared, of course) and custom messages for failed validations (for either built-in validations or custom ones), both more easily accessible than today. Also, because it is a common practice, maybe it would be nice to have a positive validation flag (to indicate when the field is non-pristine and valid) so the slot for this could be shown (or maybe just to have content on such slot would suffice). Lastly, the required state would be a separate flag, with an optional custom message for when the value is missing that would override the slot that displays the validation messages.

About having a submit button: ultimately, it could be inferred that, when we have a form on a page, a element that resembles a submit button is meant to submit the form info. To ease the programming process though a submit element is desirable. Instead of having an element just for that though, a special property flag for form submission can be put on a generic element acting as button.

## Special elements

Canvas is something that doesn't make sense in the context of content. A "box" would suffice as an element to serve as a placeholder, and all the logic is completely independent from the markup content, so no need to have an element for this.

Iframes bring very little value and too much of a security risk to be deemed necessary in the context of a new Web. Portals (basically non-interactive iframes) can be useful and can be incorporated into a new markup, although their use is much more restricted and, thus, low priority.

SVG is a nice resolution-indepent format of images and it is important that it can be used as an image format. The problem is how to use it so that its inner elements could be accessed and have their properties such as stroke color and fill changed. There would be a syntatic problem to this though, as SVG is optimised for the world of CSS generic selectors, something that would disappear.

## Lists and other repeated elements

Every other SPA framework in JS deals relatively elegantly with lists and one feature fundamental for this markup study would be to have loop-like structures for repeated elements. This would not only allow lists with less code and more efficiency, but also perhaps a native implementation of what is called "virtualisation", which is to render only the elements that are visible for when there are too many elements to render without compromising performance.

# Style

A.k.a. the current CSS resposibilities

## Style injection

Style would be injected into elements via direct property assignment or via variables (or something like "native mixins", full extendable style declarations in variables). Assigning styles would not occur via general assignments to the element type in a central file or via "classes" or any other kind of abstract selector. One simple feature that is possible to implement not to lose too much flexibility is to allow styling of the children elements from the parent.

## Element states

Because of the point explained above, these "native mixins" would have to support the styles for the current element and also "element states" (equivalent to pseudo-classes and pseudo-elements).

Instead of having a long list of pseudo-whatever properties, one may be able to just call an expression over the current element to infer a state on this element (this expression could itself be asbtracted to a variable if desired). Examples of what those expressions can capture:

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
- children (according to the point above)

It is important to mention that all states that are possible to be inferred by programming logic could be receiving their own style rules too, those above can be considered the most frequent cases but other cases may be achieved just as well.

### Media queries and @support

Media queries could be merged with the concept of element states above, so they could benefit from the flexibility and their own definition can be abstracted to variables when used frequently.

Another feature that would be there on element states is something akin to the "at-rule" `@supports`, that test the support for some rule in CSS. In this case, though, it would not be a declaration neither it would test for a style-specific functionality. As element states would support expressions, then test expressions would be supported out-of-the-box, giving a similar functionality.

## CSS global at-rules: @color, @font, @keyframes, @page



## General layout structure

# Interaction

A.k.a. the current JS resposibilities as it comes to the integration between HTML/CSS and the programmatic tree (DOM)