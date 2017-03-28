# Adding validation

---

## Validation as selectors
- Selectors can compute data from the store through function composition
- No actions for validations, less updates in the store
- Memoization benefits if using reselect

---

## Creating a character selector
- Selectors might chain multiple functions
- Each chained function returns a value
- The last function in the chain receives all returned values 

```ts
const formStateSelector = (state: IAppState) => state.form;

const characterFormSelector = createSelector(
  formStateSelector,
  (form: IForm) => form.character
);
```
- `form.character` is returned from `characterFormSelector`

---

## Validating the entire form
Check if the form has required fields

```ts
export const isFormValid = createSelector(
  characterFormSelector,
  character => character.name
    && character.bioSummary.age
    && character.skills.length > 0
);
```

---

## Add to our component

```ts
import { select } from '@angular-redux/store';
import { isFormValidSelector } from '../selectors/character';

...

@select(isFormValidSelector)
isFormValid$: Observable<boolean>;
```

After selecting, we can use `isFormValid$` with the async pipe in our template

```ts
<button 
  [disabled]="!(isFormValid$ | async)"
  type="submit">
  Save
</button>
```

---

## Field-specific validation rules
Much like we would create validators, we can create specific rules per-field

```ts
const isNameValidSelector = createSelector(
  characterNameSelector,
  ({name}) => name 
    && name.length > 3
    && name.length < 50
);
```

---

## Updating `isFormValid`
Once we have a list of validation rules for our fields, we can chain those selectors together

```ts
export const isFormValidSelector = createSelector(
  characterFormSelector,
  isNameValidSelector,
  (character, nameValid) => character.bioSummary.age
    && character.skills.length > 0  
    && nameValid
);
```

---

## Adding validation errors (1/2)
We can subscribe to the selectors we've made and use them in our template

```ts
import {
  isFormValidSelector,
  isAgeValidSelector,
  isNameValidSelector,
} from '../selectors/character';

...

@select(isAgeValidSelector)
  isAgeValid$: Observable<boolean>;
@select(isNameValidSelector)
  isNameValid$: Observable<boolean>;
```

---

## Adding validation errors (2/2)

We have access to Angular's FormControl states and we can us them to show error messages

```html
<input
  id="name"
  type="text"
  name="name"
  #nameField="ngModel"
  [(ngModel)]="characterForm.name">
<div
  [hidden]="isNameValid || nameField.control.pristine">
  Name must be between 3 and 50 characters
</div>
```
