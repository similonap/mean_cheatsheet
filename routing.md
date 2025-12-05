# Routing

## Router outlet toevoegen

In je hoofdcomponent (meestal AppComponent) voeg je de `<router-outlet>` toe. Dit is de plaats waar de componenten geladen worden op basis van de route.

```html
<!-- app.component.html -->
<router-outlet></router-outlet>
```

## Routes definieren

```ts
import { Routes } from '@angular/router';
import { HomePage } from './home-page/home-page.component';
import { AdminPage } from './about-page/admin-page.component';
export const routes: Routes = [
  {
    path: '',
    component: HomePage,
  },
  {
    path: 'admin',
    component: AdminPage,
  },
];
```

## Route parameters

```ts
import { Routes } from '@angular/router';
import { UserProfile } from './user-profile/user-profile';
const routes: Routes = [
  { path: 'user/:id', component: UserProfile }
];
```

Je kan deze parameter uitlezen aan de hand van ActivatedRoute in je component:

```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
})
export class UserProfile implements OnInit {
  userId!: string;

    constructor(private route: ActivatedRoute) {}

    ngOnInit() {
        this.userId = this.route.snapshot.paramMap.get('id')!;
    }

}
```

wil je de parameters live volgen (bijvoorbeeld bij navigatie binnen dezelfde component), dan kan je gebruik maken van een observable:

```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
})
export class UserProfile implements OnInit {
  userId!: string;
  
  constructor(private route: ActivatedRoute) {}

    ngOnInit() {
        this.route.paramMap.subscribe(params => {
            this.userId = params.get('id')!;
        });
    }
}
```

## Redirects

Je kan routes ook redirecten naar een andere route:

```ts
const routes: Routes = [    
{
    path: '',
    component: HomePage,
  },
  {
    path: 'admin',
    component: AdminPage,
  },
  { 
    path: '**', 
    redirectTo: '' 
 },
];
```

In dit voorbeeld worden alle onbekende routes (aangegeven met `**`) doorgestuurd naar de homepagina.

## Navigeren naar een andere route (via code)

Je kan navigeren naar een andere route met behulp van de Router service:

```ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-some-component',
  templateUrl: './some-component.component.html',
})
export class SomeComponent {
  constructor(private router: Router) {}
  goToAdmin() {
    this.router.navigate(['/admin']);
  }
}
```

## Navigeren naar een andere route (via template)

Je kan ook navigeren via een link in je template met behulp van de `routerLink` directive:

```html
<a routerLink="/admin">Ga naar Admin Pagina</a>
```

Vergeet dan niet de RouterModule te importeren in je module:

```ts
imports: [RouterOutlet, RouterLink, RouterLinkActive],
```

Als je een actieve link wilt stylen, kan je gebruik maken van `routerLinkActive`:

```html
<a routerLink="/admin" routerLinkActive="active-link">Ga naar Admin Pagina</a>
```

Hiermee krijgt de link de CSS-klasse `active-link` wanneer de route actief is.

Je kan ook `[routerLinkActiveOptions]` gebruiken om aan te geven of de link exact moet overeenkomen:

```html
<a routerLink="/admin" routerLinkActive="active-link" [routerLinkActiveOptions]="{ exact: true }">Ga naar Admin Pagina</a>
```