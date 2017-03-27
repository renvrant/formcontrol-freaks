# Adding validation

---

## Validation as selectors
- Selectors can compute data from the store through function composition
- No actions for validations, less updates in the store
- Memoization benefits if using reselect

---

## Creating a character selector
Selectors chain functions and pipe their return values into the final function 
`form.character` will be returned by this selector

```ts
import { IAppState } from '../store/store';
import { createSelector } from 'reselect';
import { IForm } from '../store/types';

const formStateSelector = (state: IAppState) => state.form;

const characterFormSelector = createSelector(
  formStateSelector,
  (form: IForm) => form.character
);
```

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
import { select } from 'angular-redux';
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

![Selector Flow](content/images/selector-flow.png "Selector Flow")

---

## Field-specific validation rules
Much like we would create validators, we can create specific rules per-field

```ts
export const isAgeValid = createSelector(
  bioSummarySelector,
  (bioSummary: IBioSummary) => {
    switch (bioSummary.race) {
      case 'Human': return bioSummary.age < 65;
      case 'Elf': return bioSummary.age < 800;
      case 'Tiefling': return bioSummary.age < 53;
    }
  }
);
```

---

## Generic validation functions
Often you'll want to make a selector out of re-usable validation functions

```ts
const maxStringLengthValidation = (value: string, max: number) =>
  value.length < max;
const minStringLengthValidation = (value: string, min: number) =>
  value.length > min;

export const isNameValidSelector = createSelector(
  characterFormSelector,
  (character) => maxStringLengthValidation(character.name, 50)
    && minStringLengthValidation(character.name, 3)
);
```

---

## Updating `isFormValid`
Once we have a list of validation rules for our fields, we can chain those selectors together

```ts
const isFormValidSelector = createSelector(
  isAgeValidSelector,
  isNameValidSelector,
  (ageValid, nameValid) => ageValid 
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
