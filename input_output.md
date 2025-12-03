# Parent Child Communicatie

## Child naar Parent Communicatie

```ts
// parent.component.html
<app-child [childInput]="parentData"></app-child>
```

```ts
// child.component.ts
import { Component, Input } from '@angular/core';
@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
})
export class ChildComponent {
    @Input() childInput!: string;
}
```

## Parent naar Child Communicatie

```ts
// child.component.ts
import { Component, Output, EventEmitter } from '@angular/core';
@Component({
  selector: 'app-child',
  templateUrl: './child.component.html',
})
export class ChildComponent {
    @Output() childOutput = new EventEmitter<string>();

    buttonClicked() {
        this.childOutput.emit('Data from Child');
    }
}
```

```html
<!-- child.component.html -->
<button (click)="buttonClicked()">Click me</button>
```

```ts
// parent.component.html
<app-child (childOutput)="handleChildOutput($event)"></app-child>
```

```ts
// parent.component.ts
import { Component } from '@angular/core';
@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
})
export class ParentComponent {
    handleChildOutput(data: string) {
        console.log(data);
    }
}
``` 

