# Styling

## Style via property binding

Je kan stijlen toepassen op HTML elementen via property binding met `[style.property]`:

```html
<div [style.color]="'blue'">Deze tekst is blauw</div>
<button (click)="goToAdmin()">Ga naar Admin</button>
```

of je kan deze ook dynamisch maken:

```html
<div [style.fontSize.px]="fontSize()">Deze tekst heeft dynamische grootte</div>
```

en in je component:

```ts
export class SomeComponent {
  fontSize = signal(16);

  increaseFontSize() {
    this.fontSize.update(size => size + 2);
  }
}
```

## Style via ngStyle

Je kan ook meerdere stijlen tegelijk toepassen met behulp van de `ngStyle` directive:

```html
<div [ngStyle]="{'background-color': bgColor(), 'padding.px': padding()}">
  Deze div heeft dynamische achtergrondkleur en padding
</div>
<button (click)="changeStyle()">Verander Stijl</button>
```

en in je component:

```ts
export class SomeComponent {
    bgColor = signal('lightgray');
    padding = signal(10);
    changeStyle() {
        this.bgColor.update(color => color === 'lightgray' ? 'lightblue' : 'lightgray');
        this.padding.update(pad => pad === 10 ? 20 : 10);
    }
}
```

Voor het gebruik van `ngStyle`, moet je de `NgStyle` importeren in je module of `CommonModule` gebruiken:

```ts
imports: [NgStyle],
```

of 

```ts
imports: [CommonModule],
```

## Style via class binding

Je kan ook CSS klassen toepassen via class binding met `[class.className]`:

```html
<div [class.highlighted]="isHighlighted">Deze tekst kan gemarkeerd zijn</div>
<button (click)="toggleHighlight()">Toggle Highlight</button>
```

en in je component:

```ts
export class SomeComponent {
  isHighlighted = signal(false);

  toggleHighlight() {
      this.isHighlighted.update(value => !value);
  }
}
```

de css:

```css
.highlighted {
  background-color: yellow;
}

.bold {
  font-weight: bold;
}
```

## Style via ngClass

Je kan ook meerdere CSS klassen tegelijk toepassen met behulp van de `ngClass` directive:

```html
<div [ngClass]="{'highlighted': isHighlighted(), 'bold': isBold()}">
  Deze tekst kan gemarkeerd en vetgedrukt zijn
</div>
<button (click)="toggleHighlight()">Toggle Highlight</button>
<button (click)="toggleBold()">Toggle Bold</button>
```

en in je component:

```ts
export class SomeComponent {
    isHighlighted = signal(false);
    isBold = signal(false);

    toggleHighlight() {
        this.isHighlighted.update(value => !value);
    }
    toggleBold() {
        this.isBold.update(value => !value);
    }
}
```

