# Pipes

## Ingebouwde pipes

```ts
<h3>Built-in pipes</h3>
<p>Vandaag: {{ today | date : 'fullDate' }}</p>
<p>Kort: {{ today | date : 'dd/MM/yyyy' }}</p>
<p>Tijd: {{ now | date : 'HH:mm' }}</p>
<p>Prijs: {{ 12.5 | currency : 'EUR' : 'symbol' }}</p>
<p>Met code: {{ 12.5 | currency : 'EUR' : 'code' }}</p>
<p>Formaat: {{ 1999.9 | currency : 'EUR' : 'symbol' : '1.2-2' }}</p>
```

Hoe toevoegen in component:

```ts
import { Component } from '@angular/core';
@Component({
  selector: 'app-root',
  imports: [CapitalizePipe, IbanMaskPipe, VatPipe, EuroPipe, DatePipe, CurrencyPipe],
  templateUrl: './app.component.html',    
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    today: number = Date.now();
    now: number = Date.now();
    }
```

## Eigen pipes

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'euro', standalone: true })
export class EuroPipe implements PipeTransform {
  transform(value: number, min = 2, max = 2): string {
    return new Intl.NumberFormat('nl-BE', {
      style: 'currency',
      currency: 'EUR',
      minimumFractionDigits: min,
      maximumFractionDigits: max,
    }).format(value ?? 0);
  }
}
```

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'capitalize', standalone: true })
export class CapitalizePipe implements PipeTransform {
  transform(value: string): string {
    return value ? value.charAt(0).toUpperCase() + value.slice(1) : '';
  }
}
```