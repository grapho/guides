When a component is invoked, a number of life-cycle hooks are triggered, in sequence, to properly initialize and render the component object and its corresponding DOM element.  The following hooks are a few of the most useful, for current apps.

## init()

This hook is called when the component object is first initialized.  Among it's primary function is the generation of a unique ID for the DOM element that is not yet inserted.

This method can be overwitten by calling `_super()` and is the proper place for initializing component properties, and running any additional setup functions that do not depend on any rendered DOM.

```
init() {
  this._super(...arguments);
  this.set('myFoo', []);
  this.mySetup();
}
```
_Note: Arrays and objects defined directly on a component object are shared across all instances of that component.  It is encouraged to initialize arrays and object properties during `init()` if they are meant to be unique to each instance._

## didInsertElement()

This method is triggered when the component's main element has been inserted into the DOM. It is only triggered once when the component if first invoked. In the case of a parent component rendering child components, the parent will wait until the child components bubble up their `didInsertElement()` events before trggering it's own.  This way you can always be certain of when all of the DOM becomes available.

It is at this point in the component life-cycle, when `this.$()` will become available to target with jquery.

Event listeners, jquery manipulation, and 3rd party plugin initializers can be bound to the component, by overriding this method with `_super()`.

```
didInsertElement() {
  this._super(...arguments);
  this.$().css('width', 150);
  this.$().myDatepickerLib();
}
```
_Note: While `didInsertElement()` is technically an event that can be listened for using `on('didInsertElement')`, it is strongly recommended to override the default method itself, particularly when order of execution is important._

## willDestroyElement()

When a component detects that it is time to remove itself from the DOM, `willDestroyElement` will trigger, allowing for any teardown logic to be performed.  This can be triggered by number of conditions, for instance; a wrapping if block closing `{{#if}}{{my-component}}{{/if}}`; or a parent template being torn down in response to a route transition.

```
willDestroyElement() {
  this.$().off('my-custom-event');
  this.$().myDatepickerLib().destroy();
}
```
_Note: there is no default implementation for `willDestroyElement` so `_super` is not necessary._
