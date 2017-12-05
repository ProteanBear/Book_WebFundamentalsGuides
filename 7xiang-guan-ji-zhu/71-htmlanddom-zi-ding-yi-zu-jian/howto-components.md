# HowTo: Components

> 作者：Ewa Gasperowicz、Rob Dodson、Surma

"HowTo: Components" are a collection of web components that implement common UI patterns. The purpose of these implementations is to be an educational resource. You can read through the densely commented implementation of different components and hopefully learn from them. Note that they are explicitly**NOT**a UI library and should**NOT**be used in production.

### Goals

Our aim is to demonstrate best practices for writing robust components that are accessible, performant, maintainable, and easy to style. Each component is completely self-contained so it can serve as a reference implementation.

#### Accessibility

The components closely follow the[WAI ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices-1.1/), which is a guide to explain and show ARIA, the[Accessible Rich Internet Application standard](https://www.w3.org/TR/wai-aria-1.1/). If you are unfamiliar with ARIA,[check out our introduction on WebFundamentals](../../accessibility/semantics-aria/index-hl=zh-cn.html). Each component links to the relevant section of the Authoring Practices. While not strictly necessary, we do recommend reading the section of the Authoring Practices before diving into the code.

#### Performance

In web development the term "performance" can be applied to a multitude of things. In the context of`<howto>`, performance mostly refers to animations consistently running at 60fps, even on mobile devices.

#### Visual Flexibility

As much as possible, components are not styled, except for layout or to indicate a selected or active state. This is to keep the implementation visually flexible and focused. By not spending time on decoration, we limit the code to only what is absolutely necessary to make the component function. If any style is required for the component to function, the style will be marked with a comment explaining why that is.

#### Maintainable code

As HowTo: Components is aimed to be read and function as a reference implementation, we spent extra time on writing readable and easily comprehensible code that is densely commented.

### Non-Goals

#### Be a library / framework / toolkit

`<howto>`components are not published on npm, bower or any other platform because they are not meant to be used in production. For the sake of terse, readable code, we are using modern JavaScript APIs and are supporting modern browsers which implement the Web Components standards. The idea is that you, the reader, are able adapt the code to fit your own needs after reading these implementations.

#### Be backwards compatible

The code should not be relied on directly. We might, and very likely_will_, drastically change the implementation and API of any element if a better implementation is discovered. This is a living resource where we can share, explore, and discuss best practices for building web UIs.

#### Be complete

We currently don't \(and probably won't\) implement_all_components that can be found in the WAI ARIA Authoring Practices. However, re-using the principles used in other`<howto>`components should enable readers to implement any components that are missing.

## howto-checkbox

### Summary

A`<howto-checkbox`represents a boolean option in a form. The most common type of checkbox is a dual-type which allows the user to toggle between two choices -- checked and unchecked.

The element attempts to self apply the attributes`role="checkbox"`and`tabindex="0"`when it is first created. The`role`attribute helps assistive technology like a screen reader tell the user what kind of control this is. The`tabindex`attribute opts the element into the tab order, making it keyboard focusable and operable. To learn more about these two topics, check out[What can ARIA do?](../../accessibility/semantics-aria/index-hl=zh-cn.html#what_can_aria_do)and[Using tabindex](../../accessibility/focus/using-tabindex-hl=zh-cn.html).

When the checkbox is checked, it adds a`checked`boolean attribute, and sets a corresponding`checked`property to`true`. In addition, the element sets an`aria-checked`attribute to either`"true"`or`"false"`, depending on its state. Clicking on the checkbox with a mouse, or space bar, toggles these checked states.

The checkbox also supports a`disabled`state. If either the`disabled`property is set to true or the`disabled`attribute is applied, the checkbox sets`aria-disabled="true"`, removes the`tabindex`attribute, and returns focus to the document if the checkbox is the current`activeElement`.

The checkbox is paired with a`howto-label`element to ensure it has an[accessible name](http://localhost:8080/web/fundamentals/accessibility/semantics-builtin/the-accessibility-tree#semantics_in_native_html).

> **Warning:**Just because you _can _build a custom element checkbox, doesn't necessarily mean that you _should_. As this example shows, you will need to add your own keyboard, labeling, and ARIA support. It's also important to note that the native `<form>`element will NOT submit values from a custom element. You will need to wire that up yourself using AJAX or a hidden`<input>`field. For these reasons it can often be preferable to use the built-in`<input type="checkbox">`instead.

## Reference {#reference}

* [HowTo: Components on GitHub](https://github.com/GoogleChromeLabs/howto-components)
* [Checkbox Pattern in ARIA Authoring Practices 1.1](https://www.w3.org/TR/wai-aria-practices-1.1/#checkbox)
* [What can ARIA Do?](../../accessibility/semantics-aria/index-hl=zh-cn.html#what_can_aria_do)
* [Using tabindex](../../accessibility/focus/using-tabindex-hl=zh-cn.html)

## Demo {#demo}

[View live demo on GitHub](https://googlechromelabs.github.io/howto-components/howto-checkbox/#demo)

## Example usage {#usage}

```html
<style>
  howto-checkbox {
    vertical-align: middle;
  }
  howto-label {
    vertical-align: middle;
    display: inline-block;
    font-weight: bold;
    font-family: sans-serif;
    font-size: 20px;
    margin-left: 8px;
  }
</style>

<howto-checkbox id="join-checkbox"></howto-checkbox>
<howto-label for="join-checkbox">Join Newsletter</howto-label>
```

## Code {#code}

```js
(function() {
    //Define key codes to help with handling keyboard events.
    const KEYCODE = {SPACE: 32};
    
    //Cloning contents from a <template> element is more performant 
    //than using innerHTML because it avoids additional HTML parse costs.
    const template = document.createElement('template');
    template.innerHTML = `
    <style>
      :host {
        display: inline-block;
        background: url('../images/unchecked-checkbox.svg') no-repeat;
        background-size: contain;
        width: 24px;
        height: 24px;
      }
      :host([hidden]) {
        display: none;
      }
      :host([checked]) {
        background: url('../images/checked-checkbox.svg') no-repeat;
        background-size: contain;
      }
      :host([disabled]) {
        background:
          url('../images/unchecked-checkbox-disabled.svg') no-repeat;
        background-size: contain;
      }
      :host([checked][disabled]) {
        background:
          url('../images/checked-checkbox-disabled.svg') no-repeat;
        background-size: contain;
      }
    </style>
  `;
  
  class HowToCheckbox extends HTMLElement {
    static get observedAttributes() {
      return ['checked', 'disabled'];
    }
    //The element's constructor is run anytime a new instance is created. 
    //Instances are created either by parsing HTML,
    // calling document.createElement('howto-checkbox'),
    // or calling new HowToCheckbox(); The constructor is a good place to create shadow DOM, 
    //though you should avoid touching any attributes 
    //or light DOM children as they may not be available yet.
    constructor() {
      super();
      this.attachShadow({mode: 'open'});
      this.shadowRoot.appendChild(template.content.cloneNode(true));
    }
    //connectedCallback() fires when the element is inserted into the DOM. 
    //It's a good place to set the initial role, tabindex, internal state, and install event listeners.
    connectedCallback() {
      if (!this.hasAttribute('role'))
        this.setAttribute('role', 'checkbox');
      if (!this.hasAttribute('tabindex'))
        this.setAttribute('tabindex', 0);
    //A user may set a property on an instance of an element, 
    //before its prototype has been connected to this class. 
    //The _upgradeProperty() method will check for any instance properties and run them through the proper class setters. See the lazy properties section for more details.
```



