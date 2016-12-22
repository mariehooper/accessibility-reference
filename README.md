# accessibility-reference
Reference Guide for Making Web Applications More Accessible

## Semantic Structure
- Web developer extension for Firefox shows document outline (Web developer > Information > view document outline)
- Rule of thumb for H1 - only one per page and should be most important info for page
- Landmarks - regions of webpage to provide hooks for screen readers to jump around page
- Role of search and role of navigation allow elements to become landmarks


## Buttons vs. Links
- [When to Use the Button Element](https://css-tricks.com/use-button-element/)
- [MDN Using the Button Role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_button_role)

Warning: Be careful when marking up links with the button role. Buttons are expected to be triggered using the Space key, while links are expected to be triggered through the Enter key. In other words, when links are used to behave like buttons, adding `role="button"` alone is not sufficient. It will also be necessary to add a key event handler that listens for the Space key in order to be consistent with native buttons.

## ARIA (Accessible Rich Internet Applications)
ARIA is not changing keyboard tab order, removing from DOM, anything besides including information for screen readers
- Abstract roles - inheritance, don’t use directly
Widget roles - use these
- Composite widget roles - have requirements for roles on groups of elements
- Document structure roles
- Landmark roles
- Global states and properties (e.g. aria-popup, aria-live, aria-expanded)
- Use the native HTML tags first since they provide a lot of the features needed for screen reader usage without extra work- ARIA is a fallback/addition

## Forms
### Labels
[MDN Label Reference Article](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)
- Implicit labeling: wrapping input and text up together in label element
- Using ‘for’ attribute: If these need to be separate stylistically, you can use `<label for=”element-id”>Label</label>` and give the `input` an ID that matches i.e. “element-id”

### Errors
[Using Aria-Invalid to Indicate an Error Field](https://www.w3.org/WAI/GL/wiki/Using_Aria-Invalid_to_Indicate_An_Error_Field)

`Aria-invalid` and `aria-describedby` can be used together to indicate an error in a form field

## Focus Management
- [Remove Headaches from Focus Management](https://developers.google.com/web/updates/2016/03/focus-start-point)
- [Learning to Focus()](https://www.sitepoint.com/learning-to-focus/)
- [Keyboard Navigable JavaScript Widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)

Make sure that keyboard and screen reader users are able to keep up as new items appear or are removed from the page

- Example - delete button is focused and pressed and then focus drops
- In JS - find remaining list item that has focusable delete button ( ex: add something like listItems.find(‘.btn-delete’).first().focus(); to the end of your button function)
- Also make user aware that item is being deleted - look at aria-live regions. By sending focus to next item, we are letting them know this.
- Style the focus outline by targeting [tabindex=”-1”] - this will remove the outline when I am clicking around the screen.
- You’ll still need to add tabindex="-1" (and remove the focus outline) to your named anchor targets.

## Adding Skip Links
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
