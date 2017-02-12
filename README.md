# accessibility-reference
Reference Guide for Making Web Applications More Accessible

## Table of Contents
- [What is Accessibility](#what-is-accessibility)
- [WAI-ARIA](#wai-aria)
- [Common Accessibility Patterns](#common-accessibility-patterns)
- [Semantic Structure](#semantic-structure)
- [Buttons vs. Links](#buttons-vs-links)
- [Practical Examples](#practical-examples)
  - [Accordions](#accordions)
  - [Tab Panels](#tab-panels)
  - [Forms](#forms)
  - [Focus Management](#focus-management)
  - [Skip Links](#skip-links)
  - [Tooltips](#tooltips)
- [Tools for Reviewing Accessibility](#tools-for-reviewing-accessibility)
- [Resources for Learning More](#resources-for-learning-more)


## What is Accessibility?
MDN put together a [really nice intro to accessibility](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/What_is_accessibility) as part of their 'Learn Web Development' content.

The basic gist:
- Consider accessibility from the start of a project, and test early and often. Just like any other bug, an accessibility problem becomes more expensive to fix the later it is discovered.
- Bear in mind that a lot of accessibility best practices benefit everyone, not just users with disabilities. For example, lean semantic markup is not only good for screen readers, it is also fast to load and performant, so better for everyone, especially those on mobile devices, and/or slow connections.
- Publish an accessibility statement on your site and engage with people having problems.

## WAI-ARIA
[WAI-ARIA](https://www.w3.org/TR/wai-aria-1.1/) (Web Accessibility Initiative - Accessible Rich Internet Applications) is a spec written by the W3C, defining a set of HTML attributes that can approve accessibility wherever it is lacking. Using semantic HTML will provide a lot of features needed for screen reader usage. ARIA should be a fallback or addition. ARIA is not changing keyboard tab order, removing from DOM, or doing anything besides including information for screen readers.

### There are 3 main features defined:
1. **Roles**: define what an element is or does
  - Abstract roles: used for the ontology, don’t use directly
  - Widget roles: act as standalone user interface widgets or as part of larger, composite widgets
    - Composite widget roles: have requirements for roles on groups of elements
  - Document structure roles: describe structures that organize content in a page
  - Landmark roles: regions of the page intended as navigational landmarks
  - Live region roles: may be modified by live region attributes
2. **Properties**: used to give elements extra meaning or semantics
3. **States**: Special properties that define the current conditions of elements

## Common Accessibility Patterns
Accessibility best practices that apply to web apps in general from [Angular Accessibility Developer Guide](https://docs.angularjs.org/guide/accessibility)
- **Text alternatives:** Add alternate text content to make visual information accessible using [these W3C guidelines](https://www.w3.org/TR/html-alt-techniques/). The appropriate technique depends on the specific markup but can be accomplished using offscreen spans, `aria-label` or label elements, image `alt` attributes, `figure/figcaption` elements and more.
- **HTML Semantics:** If you're creating custom element directives, Web Components or HTML in general, use native elements wherever possible to utilize built-in events and properties. Alternatively, use ARIA to communicate semantic meaning. [See notes on ARIA use](https://www.w3.org/TR/aria-in-html/#notes-on-aria-use-in-html).
- **Focus management:** Guide the user around the app as views are appended/removed. Focus should never be lost, as this causes unexpected behavior and much confusion (referred to as "freak-out mode").
- **Announcing changes:** When filtering or other UI messaging happens away from the user's focus, notify with [ARIA Live Regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions).
- **Color contrast and scale:** Make sure content is legible and interactive controls are usable at all screen sizes. Consider configurable UI themes for people with color blindness, low vision or other visual impairments.
- **Progressive enhancement:** Some users do not browse with JavaScript enabled or do not have the latest browser. An accessible message about site requirements can inform users and improve the experience.


## Semantic Structure
Creating an accessible web application starts with using semantic HTML Structure. HTML5 allows developers to create layouts with sectioning elements that are more appropriate for their content rather than simply relying on nested `<div>` elements.

Although you can make almost any HTML element behave like you want with CSS and JavaScript, using the correct element for the job often comes with things like built-in keyboard accessibility and additional functionality.

### Other benefits to semantic HTML are:
- Better SEO
- Better mobile performance
- Markup that is more easily understood

### Meaningful text labels
Make sure that your button and link text labels are understandable and distinctive. Don't just use "Click here" for your labels, as screenreader users sometimes get up a list of buttons and form controls.

### Text alternatives
Images and other non-text content are able to be described to non-sighted users in several different ways:

```HTML
<img src="dinosaur.png">

<img src="dinosaur.png"
     alt="A red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth.">

<img src="dinosaur.png"
     alt="A red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth."
     title="The Mozilla red dinosaur">


<img src="dinosaur.png" aria-labelledby="dino-label">

<p id="dino-label">The Mozilla red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth.</p>
```
### Takeaways:
- File names are read aloud by screen readers if alt text is not available, so it is important to use meaningful naming.
- The `alt` attribute should always contain descriptive information of what the image looks like visually.
- `aria-labelledby` is especially useful if you plan to use the same text as the description for multiple images.

### Accessible data tables
Screen readers are unable to associate columns and rows together as groupings of data. To accomplish this, it is important to use `<th>` to define table headers and `<scope>` to specify whether they are for columns or rows. Use the `<caption>` attribute to give a screen reader a quick summary of the table's contents.

- Web developer extension for Firefox shows document outline (Web developer > Information > view document outline)
- Rule of thumb for H1 - only one per page and should be most important info for page
- Landmarks - regions of webpage to provide hooks for screen readers to jump around page
- Role of search and role of navigation allow elements to become landmarks


## Buttons vs. Links
- [When to Use the Button Element](https://css-tricks.com/use-button-element/)
- [MDN Using the Button Role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_button_role)

Warning: Be careful when marking up links with the button role. Buttons are expected to be triggered using the Space key, while links are expected to be triggered through the Enter key. In other words, when links are used to behave like buttons, adding `role="button"` alone is not sufficient. It will also be necessary to add a key event handler that listens for the Space key in order to be consistent with native buttons.


## Practical Examples

### Accordions
[WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices-1.1/#accordion)
#### Summary of requirements for accordions:
- Each accordion header is contained in an element with role `button`. The button label is the label for the associated accordion panel.
- Each accordion header button is wrapped in an element with role `heading` that has a value set for `aria-level` that is appropriate for the information architecture of the page. If the native host language has an element with an implicit heading and aria-level, such as an HTML heading tag, a native host language element may be used. - The button element is the only element inside the heading element. That is, if there are other visually persistent elements, they are not included inside the heading element.
- If the accordion panel associated with an accordion header is visible, the **header button** element has `aria-expanded` set to `true`. If the panel is not visible, `aria-expanded` is set to `false`.
- The accordion header button element has `aria-controls` set to the ID of the element containing the accordion panel content.
- If the accordion panel associated with an accordion header is visible, and if the accordion does not permit the panel to be collapsed, the header button element has `aria-disabled` set to `true`.

#### Example HTML
```html
<div class="accordion">
  <div class="accordion-heading">
    <a role="button" href="#" aria-controls="section1" aria-expanded="true">Meats</a>
  </div>
  <div class="accordion-section" id="section1">
    <fieldset>
      <input type="checkbox" name="meat" value="pepperoni" id="meat-opt-1">
      <label for="meat-opt-1">Pepperoni</label>
      <input type="checkbox" name="meat" value="ham" id="meat-opt-2">
      <label for="meat-opt-2">Ham</label>
      <input type="checkbox" name="meat" value="sausage" id="meat-opt-3">
      <label for="meat-opt-3">Sausage</label>
    </fieldset>
  </div>
  <div class="accordion-heading">
    <a role="button" href="#" aria-controls="section2" aria-expanded="false">Vegetables</a>
  </div>
  <div class="accordion-section" id="section2">
    <fieldset>
      <input type="checkbox" name="veg" value="black olives" id="veg-opt-1">
      <label for="veg-opt-1">Black Olives</label>
      <input type="checkbox" name="veg" value="banana peppers" id="veg-opt-2">
      <label for="veg-opt-2">Banana Peppers</label>
      <input type="checkbox" name="veg" value="onions" id="veg-opt-3">
      <label for="veg-opt-3">Onions</label>
    </fieldset>
  </div>
</div>
```

### Tab Panels
[WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices-1.1/#tabpanel)

#### Summary of requirements for tab panels:
- The element that serves as the container for the set of tabs has role `tablist`.
- Each element that serves as a tab has role `tab` and is contained within the element with role `tablist`.
- Each element that contains the content panel for a tab has role `tabpanel`.
- Each element with role `tab` has the property aria-controls referring to its associated `tabpanel` element.
- The active tab element has the state aria-selected set to true and all other tab elements have it set to false.
- Each element with role `tabpanel` has the property `aria-labelledby` referring to its associated tab element.
- If a tab element has a pop-up menu, it has the property `aria-haspopup` set to true.
- If the `tablist` element is vertically oriented, it has the property aria-orientation set to vertical. The default value of aria-orientation for a `tablist` element is horizontal.

##### Example HTML
```html
<div class="tabpanel">
  <ul class="tabs" role="tablist">
    <li class="tab selected" id="tab1" role="tab" aria-controls="tabpanel1" aria-selected="true" tabindex="0">Tab 1</li>
    <li class="tab" id="tab2" role="tab" aria-controls="tabpanel2" aria-selected="false" tabindex="-1">Tab 2</li>
    <li class="tab" id="tab3" role="tab" aria-controls="tabpanel3" aria-selected="false" tabindex="-1">Tab 3</li>
  </ul>
  <div class="tabpanel selected" id="tabpanel1" aria-labelledby="tab1" aria-hidden="false">
    <p>Content</p>
  </div>
  <div class="tabpanel" id="tabpanel2" aria-labelledby="tab2" aria-hidden="true">
    <p>Content</p>
  </div>
  <div class="tabpanel" id="tabpanel3" aria-labelledby="tab3" aria-hidden="true">
    <p>Content</p>
  </div>
</div>
```
##### Example jQuery
```javascript
function showSelectedTab($tab) {
  var tabPanelId = '#' + $tab.attr('aria-controls');
  var $tabPanel = $(tabPanelId);

    // remove selected class from all tabs and assoc. tab panel other than clicked
    $tab.siblings().removeClass('selected').attr('aria-selected', 'false').attr('tabindex', '-1');
    $tabPanel.siblings().removeClass('selected').attr('aria-hidden', 'true');
    // add selected class on clicked tab and associated tab panel
    $tab.addClass('selected').attr('aria-selected', 'true').attr('tabindex', '0');
    $tabPanel.addClass('selected').attr('aria-hidden', 'false');
}

$('ul.tabs li').click(function() {
  showSelectedTab($(this));
});
```
#### Keyboard Management
According to W3C, keyboard interaction for tab panels should work as follows:
For the tab list:
- **Tab**: When the tab list is receiving focus, places focus on the active tab element. When the tab list contains the focus, moves focus to the next element in the page tab sequence outside the tablist, which is typically either the first focusable element inside the tab panel or the tab panel itself.
When focus is on a tab element in a horizontal tab list:
- **Left Arrow**: moves focus to the previous tab. If focus is on the first tab, moves focus to the last tab. Optionally, activates the newly focused tab (See note below).
- **Right Arrow**: Moves focus to the next tab. If focus is on the last tab element, moves focus to the first tab. Optionally, activates the newly focused tab (See note below).

##### Example jQuery
```javaScript
$('ul.tabs li').keydown(function(e) {
  var $tab = $(this);
  var $selectedTab = null;
  var $firstTab = $tab.parent().children().first();
  var $lastTab = $tab.parent().children().last();

  if (e.which === 37) {
    if ($tab.is($firstTab)) {
      $selectedTab = $lastTab;
    } else {
      $selectedTab = $tab.prev();
    }
  } else if (e.which === 39) {
    if ($tab.is($lastTab)) {
      $selectedTab = $firstTab;
    } else {
      $selectedTab = $tab.next();
    }
  }

  $selectedTab.focus();
  showSelectedTab($selectedTab);
});
```

### Forms
#### Labels
[MDN Label Reference Article](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label) <br>
[W3C Form Tutorial](https://www.w3.org/WAI/tutorials/forms/labels/)

##### There are two ways to label form controls:
- **Implicit labeling**: wrapping input and text up together inside label element
```HTML
<label>Username<input type="text"></label>
```
- **Explicit Labeling**: If these need to be separate stylistically, you can use the for attribute as long as it matches the ID of the form control.
```HTML
<label for="username">Username</label>
<input type="text" id="username">
```

#### Errors
[Using Aria-Invalid to Indicate an Error Field](https://www.w3.org/WAI/GL/wiki/Using_Aria-Invalid_to_Indicate_An_Error_Field)

`Aria-invalid` and `aria-describedby` can be used together to indicate an error in a form field

### Focus Management
- [Remove Headaches from Focus Management](https://developers.google.com/web/updates/2016/03/focus-start-point)
- [Learning to Focus()](https://www.sitepoint.com/learning-to-focus/)
- [Keyboard Navigable JavaScript Widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)

Make sure that keyboard and screen reader users are able to keep up as new items appear or are removed from the page

- Example - delete button is focused and pressed and then focus drops
- In JS - find remaining list item that has focusable delete button ( ex: add something like listItems.find(‘.btn-delete’).first().focus(); to the end of your button function)
- Also make user aware that item is being deleted - look at aria-live regions. By sending focus to next item, we are letting them know this.
- Style the focus outline by targeting [tabindex=”-1”] - this will remove the outline when I am clicking around the screen.
- You’ll still need to add tabindex="-1" (and remove the focus outline) to your named anchor targets.

### Skip Links
[WebAIM: Skip Navigation Links](http://webaim.org/techniques/skipnav/) <br>
Skip links are invisible anchors which can only be reached via the keyboard. They allow users to skip over main navigation elements easily while using AT.

#### How to implement these:
- Insert a link at the top of the page so that it is the first or one of the first things that is focusable.
- Give the main content (ideally a `main` element) an ID that can be targeted by the link you created.
- Update your skip link's href to match the ID of the main element.
- Style your skip link so that it hides offscreen until focused by a keyboard user.

#### Example HTML
```HTML
<body>
  <a href="#maincontent" class="skip-to-content">Skip to main content</a>
  <main id="maincontent">
```

#### Example SCSS
```SCSS
.skip-to-content a {
  padding: 6px
  position: absolute
  top: -40px
  left: 0px
  color: $color-white
  border-right: 1px solid $color-white
  border-bottom: 1px solid $color-white
  border-bottom-right-radius: 8px
  background: transparent
  transition: top 1s ease-out, background 1s linear
  z-index: 100
  &:focus {
    top: 0px
    background: $color-red-1
    outline: 0
    -webkit-transition: top .1s ease-in, background .5s linear
    transition: top .1s ease-in, background .5s linear
  }
}
```
### Tooltips
- The element that serves as the **tooltip container** has role `tooltip`.
- The element that triggers the tooltip references the tooltip element with `aria-describedby`.

## Tools for Reviewing Accessibility
- [Accessibility Developer Tools Chrome Extension](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en)
- Safari > dev tools > elements > node > accessibility
- Voiceover on Mac: 
  - Ctrl Opt Command + H - cycles through headings on page
  - Voiceover Rotor - Ctrl Option + U
- Chrome://accessibility - shows all open tabs and can toggle accessibility tree on

## Resources for Learning More
### Videos
- [Start Building Accessible Web Applications Today](https://egghead.io/courses/start-building-accessible-web-applications-today)
- [A Beginner's Guide to Web Accessibility](https://webdesign.tutsplus.com/courses/a-beginners-guide-to-web-accessibility)
- [Google's Introduction to Web Accessibility](https://webaccessibility.withgoogle.com/course)

### Books
- [Inclusive Design Patterns by Heydon Pickering](https://www.smashingmagazine.com/inclusive-design-patterns/)

### Websites
- [WebAIM](http://webaim.org/intro/)
- [Web Accessibility Resources from Marcy Sutton](https://marcysutton.com/web-accessibility-resources/)
- [Simply Accessible Articles](http://simplyaccessible.com/articles/)
- [Accessibility Weekly by David A. Kennedy](http://a11yweekly.com/)
