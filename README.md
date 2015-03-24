red-draggin
===========================

## Disclaimer

**This is a fork of the excellent [angular-drag-and-drop-lists](https://github.com/marceljuenemann/angular-drag-and-drop-lists).**

The original project is extremely well-written, but didn't quite support our use case.  Unfortunately, the proposed changes would break backwards compatibility in such a way that forking was necessary.

Differences:

- Moving & copying are no longer destructive operations
  - Moving does not destroy object references or prototype chains
  - Copying actually depply clones the dragged object
- Doesn't really support dragging stuff from the desktop
- New dependency: [angular-types](https://github.com/decipherinc/angular-types) for cloning support

This deviates from the HTML5 drag-and-drop API, as that API uses (recommends? I don't even know) JSON for object transport.

Thus, this library becomes a *general-purpose* AngularJS drag-and-drop library, but does not support things like dragging files from your desktop.  Or not yet, anyway.  I don't think.

## Description

Angular directives that allow you to build sortable lists with the native HTML5 drag & drop API. The directives can also be nested to bring drag & drop to your WYSIWYG editor, your tree, or whatever fancy structure you are building.

## Demo
- [Nested Lists](http://decipherinc.github.io/red-draggin/demo/#/nested) 
- [Simple Lists](http://decipherinc.github.io/red-draggin/demo/#/simple) 
- [Typed Lists](http://decipherinc.github.io/red-draggin/demo/#/types) 
- [Advanced Features](http://decipherinc.github.io/red-draggin/demo/#/advanced) 

## Why another drag & drop library?
There are tons of other drag & drop libraries out there, but none of them met my three requirements:

- **Angular:** If you use angular.js, you really don't want to throw a bunch of jQuery into your app. Instead you want to use libraries that were build the "angular way" and support **two-way data binding** to update your data model automatically.
- **Nested lists:** If you want to build a **WYSIWYG editor** or have some fancy **tree structure**, the library has to support nested lists.
- **HTML5 drag & drop:** Most drag & drop applications you'll find on the internet use pure JavaScript drag & drop. But with the arrival of HTML5 we can delegate most of the work to the browser. For example: If you want to show the user what he's currently dragging, you'll have to update the position of the element all the time and set it below the mouse pointer. In HTML5 the browser will do that for you! But you can not only save code lines, you can also offer a more **native user experience**: If you click on an element in a pure JavaScript drag & drop implementation, it will usually start the drag operation. But remember what happens when you click an icon on your desktop: The icon will be selected, not dragged! This is the native behaviour you can bring to your web application with HTML5.

If this doesn't fit your requirements, check out one of the other awesome drag & drop libraries:

- [angular-drag-and-drop-lists](https://github.com/marceljuenemann/angular-drag-and-drop-lists): The project upon which this project was forked.
- [angular-ui-tree](https://github.com/JimLiu/angular-ui-tree): Very similar to this library, but does not use the HTML5 API. Therefore you need to write some more markup to see what you are dragging and it will create another DOM node that you have to style. However, if you plan to support touch devices this is probably your best choice.
- [angular-dragdrop](https://github.com/ganarajpr/angular-dragdrop): One of many libraries with the same name. This one uses the HTML5 API, but if you want to build (nested) sortable lists, you're on your own, because it does not calculate the correct element position for you.
- [angular-dragon-drop](https://github.com/btford/angular-dragon-drop): Very simple sorting directive.
- [more...](https://www.google.de/search?q=angular+drag+and+drop)


## Supported browsers

Internet Explorer 8 or lower is *not supported*, but all modern browsers are (see changelog for tested versions).

Note that **touch devices are not supported**, because the HTML5 drag & drop standard doesn't cover those.


## Usage

### Download & Installation
Download `red-draggin.js` (or the minified version) and include it in your application. If you use bower, you can of course just add it via bower. Add the `fv.red-draggin` module as dependency to your angular app.

### `draggable` directive
Use the `draggable` directive to make your element draggable

-*Attributes**
- `draggable` Required attribute. The value has to be an object that represents the data of the element. In case of a drag and drop operation the object will be serialized and unserialized on the receiving end.
- `dnd-selected` Callback that is invoked when the element was clicked but not dragged. The original click event will be provided in the local `event` variable. ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/nested)~~
- `dnd-effect-allowed` Use this attribute to limit the operations that can be performed. Options are:
    * `move` The drag operation will move the element. This is the default
    * `copy` The drag operation will copy the element. There will be a copy cursor.
    * `copyMove` The user can choose between copy and move by pressing the ctrl or shift key.
        * *Not supported in IE:* In Internet Explorer this option will be the same as `copy`. 
        * *Not fully supported in Chrome on Windows:* In the Windows version of Chrome the cursor will always be the move cursor. However, when the user drops an element and has the ctrl key pressed, we will perform a copy anyways.
    * HTML5 also specifies the `link` option, but this library does not actively support it yet, so use it at your own risk.
    * ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-moved` Callback that is invoked when the element was moved. Usually you will remove your element from the original list in this callback, since the directive is not doing that for you automatically. The original dragend event will be provided in the local `event` variable. ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-copied` Same as dnd-moved, just that it is called when the element was copied instead of moved. The original dragend event will be provided in the local `event` variable. ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-dragstart` Callback that is invoked when the element was dragged. The original dragstart event will be provided in the local `event` variable. ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)
- `dnd-type` Use this attribute if you have different kinds of items in your application and you want to limit which items can be dropped into which lists. Combine with dnd-allowed-types on the droppable(s). This attribute should evaluate to a string, although this restriction is not enforced (at the moment) ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/types)~~ 
- `dnd-disable-if` You can use this attribute to dynamically disable the draggability of the element. This is useful if you have certain list items that you don't want to be draggable, or if you want to disable drag & drop completely without having two different code branches (e.g. only allow for admins). *Note*: If your element is not draggable, the user is probably able to select text or images inside of it. Since a selection is always draggable, this breaks your UI. You most likely want to disable user selection via CSS (see [user-select](http://stackoverflow.com/a/4407335)) ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/types)~~ 

-*CSS classes**
- `dndDragging` This class will be added to the element while the element is being dragged. It will affect both the element you see while dragging and the source element that stays at it's position. Do not try to hide the source element with this class, because that will abort the drag operation.
- `dndDraggingSource` This class will be added to the element after the drag operation was started, meaning it only affects the original element that is still at it's source position, and not the "element" that the user is dragging with his mouse pointer

### `droppable` directive
Use the droppable attribute to make your list element a dropzone. Usually you will add a single li element as child with the ng-repeat directive. If you don't do that, we will not be able to position the dropped element correctly. If you want your list to be sortable, also add the dnd-draggable directive to your li element(s). Both the droppable and it's direct children must have position: relative CSS style, otherwise the positioning algorithm will not be able to determine the correct placeholder position in all browsers.

-*Attributes**
- `droppable` Required attribute. The value has to be the array in which the data of the dropped element should be inserted.
- `dnd-allowed-types` Optional array of allowed item types. When used, only items that had a matching dnd-type attribute will be dropable ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/types)~~ 
- `dnd-disable-if` Optional boolean expresssion. When it evaluates to true, no dropping into the list is possible. Note that this also disables rearranging items inside the list ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/types)~~ 
- `dnd-horizontal-list` Optional boolean expresssion. When it evaluates to true, the positioning algorithm will use the left and right halfs of the list items instead of the upper and lower halfs ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-dragover` Optional expression that is invoked when an element is dragged over the list. If the expression is set, but does not return true, the element is not allowed to be dropped. The following variables will be available:
    * `event` The original dragover event sent by the browser.
    * `index` The position in the list at which the element would be dropped.
    * `type` The `dnd-type` set on the dnd-draggable, or undefined if unset.
    *~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-drop` Optional expression that is invoked when an element is dropped over the list. If the expression is set, it must return the object that will be inserted into the list. If it returns false, the drop will be aborted and the event is propagated. The following variables will be available:
    * `event` The original drop event sent by the browser.
    * `index` The position in the list at which the element would be dropped.
    * `item` The transferred object.
    * `type` The dnd-type set on the dnd-draggable, or undefined if unset.
    *~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~
- `dnd-external-sources` Optional boolean expression. When it evaluates to true, the list accepts drops from sources outside of the current browser tab. This allows to drag and drop accross different browser tabs. Note that this will allow to drop arbitrary text into the list, thus it is highly recommended to implement the dnd-drop callback to check the incoming element for sanity. Furthermore, the dnd-type of external sources can not be determined, therefore do not rely on restrictions of dnd-allowed-type. Also note that this feature does not work very well in Internet Explorer ~~[Demo](http://decipherinc.github.io/red-draggin/demo/#/advanced)~~

-*CSS classes**
- `dndPlaceholder` When an element is dragged over the list, a new placeholder child element will be added. This element is of type li and has the class dndPlaceholder set.
- `dndDragover` This class will be added to the list while an element is being dragged over the list.

### Required CSS styles 
Both the droppable and it's children require relative positioning, so that the directive can determine the mouse position relative to the list and thus calculate the correct drop position.

```css
ul[droppable], ul[droppable] > li {
  position: relative;
}
```

**Since 1.2.0 `pointer-events: none` is no longer required**

### Example

~~Take a look at the code in the [Simple Lists](http://decipherinc.github.io/red-draggin/demo/#/simple) example~~

## TODO

- Further cleanup this file
- Tests and lint checks
- Assert compatibility with HTML5 drag and drop API
- Assert browser compatibility
- Publish demo site
- Simply all attribute names
- Assert compatibility with non-`ul`/`li` tags
- Simplify object transport

## Maintainer

[Christopher Hiller](http://focusvision.com)

## License

Copyright (c) 2014 Marcel Juenemann

[MIT License](https://raw.githubusercontent.com/decipherinc/red-draggin/master/LICENSE)
