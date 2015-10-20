What makes a component such a useful tool is that it is closely tied to your app's templates and the DOM itself.  Components are the primary object to use for direct DOM manipulation, listening and responding to browser events, and binding 3rd party JS libraries into your Ember app.

Though to get the most use out of a component it is important to understand it's "life-cycle" methods. The following hooks are a few of the most useful and commonly used, for current apps.

## Binding to the Component Element

Suppose you want to integrate your favorite datepicker library into an Ember project. Typically, 3rd party JS/Jquery libraries require a DOM element to bind itself to. So, where is the best place to initialize the library?

When a component successfully renders its backing DOM element into the DOM, it will trigger it's `didInsertElement()` hook.  It is at this point in the component life-cycle, when `this.$()` will become available to target with jquery. `this.$()` will, by default, return the component's main element, but it is also valid to target child elements withing the component's template as well: `this.$('.some-css-selector')`.

So let's initialize our datepicker by overriding this method with `_super()`.  The date pickers usually attach to an `<input>` so we will find an input within our component's template.

```components/my-component.js
didInsertElement() {
  this._super(...arguments);
  this.$('input').myDatepickerLib();
}
```

`didInsertElement()` is also a place to bind event listeners.  this is particialrly useful for custom events or other browser events which do not have a [built-in event handler](http://guides.emberjs.com/v2.1.0/components/handling-events/#toc_event-names).  Perhaps you have some custom CSS animations trigger when the component is rendered and you want to handle some cleanup when it ends?

```components/my-component.js
didInsertElement() {
  this._super(...arguments);
  this.$().on('animationend', () => {
    $(this).removeClass('.sliding-anim');
  });
}
```

There are a few things to note about the `didInsertElement()` hook:

- It is only triggered once when the component if first invoked and the element is rendered. For logic that needs to trigger on a re-render, consider using `didRender()`
- In the case of a parent component rendering child components, the parent will wait until the child components bubble up their respective `didInsertElement()` events before triggering it's own.
- Setting component properties with `set()` during `didInsertElement()` has historically led to poor rendering performance and has been deprecated in recent versions of Ember.
- While `didInsertElement()` is technically an event that can be listened for using `on('didInsertElement')`, it is strongly encouraged to override the default method itself, particularly when order of execution is important.

## Clean-up and Teardown of Component Elements

When a component detects that it is time to remove itself from the DOM, `willDestroyElement` will trigger, allowing for any teardown logic to be performed.  This can be triggered by number of conditions, for instance; a wrapping `{{if` block closing `{{#if falseBool}}{{my-component}}{{/if}}`; or a parent template being torn down in response to a route transition.

Let's use that hook to cleanup our date picker and event listener from above:

```components/my-component.js
willDestroyElement() {
  this.$().off('animationend');
  this.$().myDatepickerLib().destroy();
}
```
_Note: there is no default implementation for `willDestroyElement` so `_super` is not necessary._
