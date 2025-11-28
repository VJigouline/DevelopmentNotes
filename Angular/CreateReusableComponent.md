# Creating Reusable Angular Components

This guide explains how to design and build reusable, maintainable Angular components using `@Input()`, `@Output()`, lifecycle hooks, and best practices.

## Key principles

- **Single Responsibility**: Each component does one thing well.
- **Input/Output Communication**: Use `@Input()` for parent→child data, `@Output()` for child→parent events.
- **No hard-coded data**: Accept data via `@Input()` properties.
- **Encapsulation**: Use `ViewEncapsulation` to isolate styles.
- **Change Detection**: Optimize with `OnPush` for better performance.

## 1) Generate a component

```powershell
ng generate component components/button
# or:
ng g c components/button
```

This creates:
- `button.component.ts` — component class
- `button.component.html` — template
- `button.component.css` — styles
- `button.component.spec.ts` — unit tests

## 2) Define inputs and outputs

**button.component.ts**:

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-button',
  templateUrl: './button.component.html',
  styleUrls: ['./button.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ButtonComponent {
  @Input() label: string = 'Click me';
  @Input() disabled: boolean = false;
  @Input() type: 'primary' | 'secondary' | 'danger' = 'primary';
  
  @Output() onClick = new EventEmitter<void>();

  onButtonClick(): void {
    this.onClick.emit();
  }
}
```

**button.component.html**:

```html
<button 
  [class]="'btn btn-' + type"
  [disabled]="disabled"
  (click)="onButtonClick()">
  {{ label }}
</button>
```

**button.component.css**:

```css
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  background-color: #007bff;
  color: white;
}

.btn-primary:hover:not(:disabled) {
  background-color: #0056b3;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}

.btn-danger {
  background-color: #dc3545;
  color: white;
}
```

## 3) Use the component in a parent

**app.component.html**:

```html
<div>
  <app-button 
    [label]="'Submit'" 
    [type]="'primary'"
    (onClick)="handleSubmit()">
  </app-button>

  <app-button 
    [label]="'Cancel'" 
    [type]="'secondary'"
    (onClick)="handleCancel()">
  </app-button>

  <app-button 
    [label]="'Delete'" 
    [type]="'danger'"
    [disabled]="isDeleting">
  </app-button>
</div>
```

**app.component.ts**:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  isDeleting = false;

  handleSubmit(): void {
    console.log('Submit clicked');
  }

  handleCancel(): void {
    console.log('Cancel clicked');
  }
}
```

## 4) Advanced: Input validation and lifecycle hooks

**card.component.ts** (more complex example):

```typescript
import { 
  Component, 
  Input, 
  Output, 
  EventEmitter, 
  OnInit, 
  OnChanges, 
  SimpleChanges,
  ChangeDetectionStrategy 
} from '@angular/core';

@Component({
  selector: 'app-card',
  templateUrl: './card.component.html',
  styleUrls: ['./card.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CardComponent implements OnInit, OnChanges {
  @Input() title: string = '';
  @Input() content: string = '';
  @Input() imageUrl: string | null = null;
  @Input() isLoading: boolean = false;

  @Output() onClose = new EventEmitter<void>();

  ngOnInit(): void {
    if (!this.title) {
      console.warn('CardComponent: title is required');
    }
  }

  ngOnChanges(changes: SimpleChanges): void {
    if (changes['title']) {
      console.log('Title changed to:', changes['title'].currentValue);
    }
  }

  close(): void {
    this.onClose.emit();
  }
}
```

**card.component.html**:

```html
<div class="card">
  <div class="card-header">
    <h3>{{ title }}</h3>
    <button class="close-btn" (click)="close()">×</button>
  </div>
  <div class="card-body">
    <img *ngIf="imageUrl" [src]="imageUrl" alt="Card image" />
    <div *ngIf="isLoading" class="loading">Loading...</div>
    <p *ngIf="!isLoading">{{ content }}</p>
  </div>
</div>
```

## 5) Two-way binding with ngModel

For components that manage input state, support two-way binding:

**input.component.ts**:

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-input',
  templateUrl: './input.component.html',
  styleUrls: ['./input.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class InputComponent {
  @Input() value: string = '';
  @Output() valueChange = new EventEmitter<string>();

  onInput(event: Event): void {
    const input = event.target as HTMLInputElement;
    this.valueChange.emit(input.value);
  }
}
```

**Usage with two-way binding** (`[()]` syntax):

```html
<app-input [(value)]="userName"></app-input>
<p>User name: {{ userName }}</p>
```

## 6) Content projection (ng-content)

Make components even more flexible with content projection:

**card.component.ts**:

```typescript
import { Component, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-card',
  templateUrl: './card.component.html',
  styleUrls: ['./card.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CardComponent {}
```

**card.component.html**:

```html
<div class="card">
  <div class="card-header">
    <ng-content select=".card-header"></ng-content>
  </div>
  <div class="card-body">
    <ng-content select=".card-body"></ng-content>
  </div>
  <div class="card-footer">
    <ng-content select=".card-footer"></ng-content>
  </div>
</div>
```

**Usage**:

```html
<app-card>
  <h2 class="card-header">My Card</h2>
  <p class="card-body">This is the content.</p>
  <button class="card-footer">Action</button>
</app-card>
```

## 7) Best practices checklist

✅ **Do:**
- Use `@Input()` for immutable data, `@Output()` for events.
- Validate `@Input()` values in `ngOnInit()` or `ngOnChanges()`.
- Use `ChangeDetectionStrategy.OnPush` for performance.
- Document inputs/outputs with JSDoc comments.
- Write unit tests for component logic.
- Keep components small and focused (single responsibility).
- Use `[class]` or `[ngClass]` for dynamic styling over direct `style` manipulation.

❌ **Don't:**
- Mutate `@Input()` properties directly; emit `@Output()` events instead.
- Make components depend on global state without a clear interface.
- Hard-code strings or magic numbers; use `@Input()` instead.
- Overuse two-way binding; prefer explicit one-way data flow.
- Forget to unsubscribe from observables (use `async` pipe or `takeUntil()`).

## 8) Example: Complete reusable form input component

**form-input.component.ts**:

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-form-input',
  template: `
    <div class="form-group">
      <label>{{ label }}</label>
      <input 
        [type]="type"
        [placeholder]="placeholder"
        [value]="value"
        [disabled]="disabled"
        (input)="onInput($event)"
        (blur)="onBlur()"
      />
      <small *ngIf="error" class="error">{{ error }}</small>
    </div>
  `,
  styles: [`
    .form-group {
      margin-bottom: 16px;
    }
    label {
      display: block;
      font-weight: 500;
      margin-bottom: 4px;
    }
    input {
      width: 100%;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    input:focus {
      outline: none;
      border-color: #007bff;
      box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.1);
    }
    .error {
      color: #dc3545;
      display: block;
      margin-top: 4px;
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FormInputComponent {
  @Input() label: string = '';
  @Input() type: string = 'text';
  @Input() placeholder: string = '';
  @Input() value: string = '';
  @Input() disabled: boolean = false;
  @Input() error: string | null = null;

  @Output() valueChange = new EventEmitter<string>();
  @Output() onBlurEvent = new EventEmitter<void>();

  onInput(event: Event): void {
    const input = event.target as HTMLInputElement;
    this.valueChange.emit(input.value);
  }

  onBlur(): void {
    this.onBlurEvent.emit();
  }
}
```

## 9) Testing reusable components

**button.component.spec.ts**:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ButtonComponent } from './button.component';

describe('ButtonComponent', () => {
  let component: ButtonComponent;
  let fixture: ComponentFixture<ButtonComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ ButtonComponent ]
    })
    .compileComponents();

    fixture = TestBed.createComponent(ButtonComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should emit onClick event when button is clicked', () => {
    spyOn(component.onClick, 'emit');
    const button = fixture.nativeElement.querySelector('button');
    button.click();
    expect(component.onClick.emit).toHaveBeenCalled();
  });

  it('should disable button when disabled=true', () => {
    component.disabled = true;
    fixture.detectChanges();
    const button = fixture.nativeElement.querySelector('button');
    expect(button.disabled).toBe(true);
  });
});
```

## 10) Useful resources

- Angular Input/Output: https://angular.io/guide/inputs-outputs
- Component interaction: https://angular.io/guide/component-interaction
- Content projection: https://angular.io/guide/content-projection
- Change detection: https://angular.io/guide/change-detection
- Testing components: https://angular.io/guide/component-testing

---
**Key takeaway:**  
Build reusable components by accepting data via `@Input()`, emitting events via `@Output()`, keeping logic simple, and documenting the public API. This makes components composable, testable, and easy to share across your application.
