# accessibility-reference
Reference Guide for Making Web Applications More Accessible

Table of Contents

- [Common Accessibility Patterns](#common-accessibility-patterns)
- [Semantic Structure](#semantic-structure)
- [Buttons vs. Links](#buttons-vs-links)
- [ARIA](#aria)
- [Practical Examples](#practical-examples)
  - [Accordions](#Accordions)
  - [Tab Panels](#Tab-panels)
  - [Forms](#forms)
  - [Focus Management](#focus-management)
  - [Adding Skip Links](#adding-skip-links)
- [Tools for Reviewing Accessibility](#tools-for-reviewing-accessibility)
- [Resources for Learning More](#resources-for-learning-more)


## Common Accessibility Patterns
Accessibility best practices that apply to web apps in general from [Angular Accessibility Developer Guide](https://docs.angularjs.org/guide/accessibility)
- **Text alternatives:** Add alternate text content to make visual information accessible using [these W3C guidelines](https://www.w3.org/TR/html-alt-techniques/). The appropriate technique depends on the specific markup but can be accomplished using offscreen spans, `aria-label` or label elements, image `alt` attributes, `figure/figcaption` elements and more.
- **HTML Semantics:** If you're creating custom element directives, Web Components or HTML in general, use native elements wherever possible to utilize built-in events and properties. Alternatively, use ARIA to communicate semantic meaning. [See notes on ARIA use](https://www.w3.org/TR/aria-in-html/#notes-on-aria-use-in-html).
- **Focus management:** Guide the user around the app as views are appended/removed. Focus should never be lost, as this causes unexpected behavior and much confusion (referred to as "freak-out mode").
- **Announcing changes:** When filtering or other UI messaging happens away from the user's focus, notify with [ARIA Live Regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions).
- **Color contrast and scale:** Make sure content is legible and interactive controls are usable at all screen sizes. Consider configurable UI themes for people with color blindness, low vision or other visual impairments.
- **Progressive enhancement:** Some users do not browse with JavaScript enabled or do not have the latest browser. An accessible message about site requirements can inform users and improve the experience.


## Semantic Structure
- Web developer extension for Firefox shows document outline (Web developer > Information > view document outline)
- Rule of thumb for H1 - only one per page and should be most important info for page
- Landmarks - regions of webpage to provide hooks for screen readers to jump around page
- Role of search and role of navigation allow elements to become landmarks


## Buttons vs. Links
- [When to Use the Button Element](https://css-tricks.com/use-button-element/)
- [MDN Using the Button Role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_button_role)

Warning: Be careful when marking up links with the button role. Buttons are expected to be triggered using the Space key, while links are expected to be triggered through the Enter key. In other words, when links are used to behave like buttons, adding `role="button"` alone is not sufficient. It will also be necessary to add a key event handler that listens for the Space key in order to be consistent with native buttons.

## ARIA
ARIA (Accessible Rich Internet Applications) is not changing keyboard tab order, removing from DOM, anything besides including information for screen readers
- Abstract roles - inheritance, don’t use directly
Widget roles - use these
- Composite widget roles - have requirements for roles on groups of elements
- Document structure roles
- Landmark roles
- Global states and properties (e.g. aria-popup, aria-live, aria-expanded)
- Use the native HTML tags first since they provide a lot of the features needed for screen reader usage without extra work- ARIA is a fallback/addition

## Practical Examples

### Accordions


### Tab Panels
[WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices-1.1/#tabpanel)

Summary of requirements for tab panels:
- The element that serves as the container for the set of tabs has role `tablist`.
- Each element that serves as a tab has role `tab` and is contained within the element with role `tablist`.
- Each element that contains the content panel for a tab has role `tabpanel`.
- Each element with role `tab` has the property aria-controls referring to its associated `tabpanel` element.
- The active tab element has the state aria-selected set to true and all other tab elements have it set to false.
- Each element with role `tabpanel` has the property `aria-labelledby` referring to its associated tab element.
- If a tab element has a pop-up menu, it has the property `aria-haspopup` set to true.
- If the `tablist` element is vertically oriented, it has the property aria-orientation set to vertical. The default value of aria-orientation for a `tablist` element is horizontal.

#### Example HTML
```
<div class="tabpanel">
  <ul class="tabs selected" role="tablist">
    <li class="tab" id="tab1" role="tab" aria-controls="tabpanel1" aria-selected="true">Tab 1</li>
    <li class="tab" id="tab2" role="tab" aria-controls="tabpanel2" aria-selected="false">Tab 2</li>
    <li class="tab" id="tab3" role="tab" aria-controls="tabpanel3" aria-selected="false">Tab 3</li>
  </ul>
  <div class="tabpanel" id="tabpanel1" aria-labelledby="tab1" aria-hidden="false">
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
#### Example jQuery
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

Example jQuery
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
[MDN Label Reference Article](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)
- Implicit labeling: wrapping input and text up together in label element
- Using ‘for’ attribute: If these need to be separate stylistically, you can use `<label for=”element-id”>Label</label>` and give the `input` an ID that matches i.e. “element-id”

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

### Adding Skip Links
Skip links are invisible anchors which can only be reached via the keyboard.
How to implement these:
- Add ul - with links like `#main` to get to main content and `#footer` to get to global footer (make sure these elements have matching IDs) and add `tabindex="-1"` so that focus is sent to appropriate area.
- Set ul `position: absolute`
- For anchors inside, `display: block`, `position: absolute` and `left: -99999px`, add padding, `background-color: white`
- Make visible on focus with pseudo element: `ul.skip-links a:focus {left: 0}`

## Tools for Reviewing Accessibility
- [Accessibility Developer Tools Chrome Extension](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en)
- Safari > dev tools > elements > node > accessibility
- Voiceover on Mac: 
  - Ctrl Opt Command + H - cycles through headings on page
  - Voiceover Rotor - Ctrl Option + U
- Chrome://accessibility - shows all open tabs and can toggle accessibility tree on

## Resources for Learning More
