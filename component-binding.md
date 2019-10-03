# Component Binding

https://ultimatecourses.com/blog/passing-data-angular-2-components-input

`app.component.ts`

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
export class AppComponent {
  initialCount: number = 10;
}
```

The difference in creating our own properties to bind to (as opposed to a built-in property) is we have to *tell* Angular the name of the property binding, essentially exposing it for us to bind to.



create a binding called `count` on our component and pass *through* our `initialCount` value:

```typescript
@Component({
  selector: 'app-root',
  template: `
    <div class="app">
      <counter [count]="initialCount"></counter>
    </div>
  `
})
export class AppComponent {
  initialCount: number = 10;
}
```





### @Input decorator, stateless component

Now we’re creating a stateless, or “dumb” component, to pass our data *into*, which we can mutate locally and get data back *out*. We’ll be getting new data back out of the component in the next article.

```typescript
import { Component, Input } from '@angular/core';

@Component({...})
export class CounterComponent {

  @Input()
  count: number = 0;

  increment() {
    this.count++;
  }

  decrement() {
    this.count--;
  }

}
```

This decorator tells Angular to treat `count` as an input binding, and if a piece of data is supplied, the count is then used – otherwise it will default to the value of 0 we added inside the child component above.







### Bonus: custom property names

It may be that you’d want your “public” property names to differ from the internal input names. Here’s what we might want to do:



```typescript
@Component({
  selector: 'app-root',
  template: `
   <div class="app">
      <counter [init]="initialCount"></counter>
    </div>
  `
})
export class AppComponent {
  initialCount: number = 10;
}
```

```typescript
@Component({...})
export class CounterComponent {

  @Input('init')
  count: number = 0;

  // ...

}
```



### Stateful (parent) component binding

@Input`  decorator to accept an input binding, we can do the same and listen in  the parent for when a value changes inside our child component.

To do this, we’ll head back to our parent component that’s rendering out our count:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <div class="app">
      Parent: {{ myCount }}
      <counter
        [count]="myCount"
        (change)="countChange($event)">
      </counter>
    </div>
  `
})
export class AppComponent {
  myCount: number = 10;
  countChange(event) {

  }
}
```



### @Output decorator

Much like using `Input`, we can import `Output` and decorate a new `change` property inside our `CounterComponent`

```typescript
import { Component, Input, Output } from '@angular/core';

@Component({...})
export class CounterComponent {

  @Input()
  count: number = 0;

  @Output()
  change;

  // ...

}
```

### EventEmitter

This is the interesting part. To be able to use our `Output`, we need to import and bind a new instance of the `EventEmitter` to it:

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';

@Component({...})
export class CounterComponent {

  // ...

  @Output()
  change = new EventEmitter();

  // ...

}
```

Using TypeScript to the fullest we’d do something like this to signify the *type* of event value we are emitting, and our `change` output is of type `EventEmitter`. In our case we are emitting a `number` type:

```typescript
 @Output()
  change: EventEmitter<number> = new EventEmitter<number>();
```



### Invoking the EventEmitter

So what’s happening here? We’ve created a `change` property, and bound a new instance of `EventEmitter` to it – what next?

We can simply call our `this.change` method – however because it references an instance of `EventEmitter`, we have to call `.emit()` to emit an event to the parent:

```typescript
@Component({...})
export class CounterComponent {

  @Input()
  count: number = 0;

  @Output()
  change: EventEmitter<number> = new EventEmitter<number>();

  increment() {
    this.count++;
    this.change.emit(this.count);
  }

  decrement() {
    this.count--;
    this.change.emit(this.count);
  }

}
```



### Bonus: custom property names

Much [like we learned](https://ultimatecourses.com/blog/passing-data-angular-2-components-input#bonus-custom-property-names) with `@Input()` and creating custom property names, we can also do the same with `@Output()`.

Let’s assume that we change the `(change)` binding to `(update)`:

```typescript
@Component({
  selector: 'app-root',
  template: `
    <div class="app">
      Parent: {{ myCount }}
      <counter
        [count]="myCount"
        (update)="countChange($event)">
      </counter>
    </div>
  `
})
export class AppComponent {
  myCount: number = 10;
  countChange(event) {
    this.myCount = event;
  }
}
```

```typescript
@Component({...})
export class CounterComponent {

  // ...

  @Output('update')
  change: EventEmitter<number> = new EventEmitter<number>();

  increment() {
    this.count++;
    this.change.emit(this.count);
  }

  decrement() {
    this.count--;
    this.change.emit(this.count);
  }

}
```

