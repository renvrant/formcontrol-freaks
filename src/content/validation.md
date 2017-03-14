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
For now let's just check for required fields

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
import { select } from 'ng2-redux';
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
const humanAgeValid = isBetweenNumber(14, 40);
const elfAgeValid = isBetweenNumber(80, 800);
const tieflingAgeValid = isBetweenNumber(35, 53);

const ageValidationSelector = createSelector(
  bioSummarySelector,
  (bioSummary: IBioSummary) => {
    switch (bioSummary.race) {
      case 'Human': return humanAgeValid;
      case 'Elf': return elfAgeValid;
      case 'Tiefling': return tieflingAgeValid;
    }
  }
);

export const isAgeValidSelector = createSelector(
  bioSummarySelector,
  ageValidationSelector,
  ({age}, isAgeValid) => isAgeValid(age)
);
```

---

## Updating `isFormValid`
Once we have a list of validation rules for our fields, we can chain those selectors together

```ts
export const isFormValidSelector = createSelector(
  isAgeValidSelector,
  isNameValidSelector,
  (ageValid, nameValid) => ageValid 
    && nameValid
```

---

## Generic validation
Often you'll want to make a selector out of re-usable validation functions

```ts
export const maxStringLengthValidation = (value: string, max: number) =>
  value.length < max;
export const minStringLengthValidation = (value: string, min: number) =>
  value.length > min;

export const isNameValidSelector = createSelector(
  createFormFieldSelector(['character', 'name']),
  (name: string) => maxStringLengthValidation(name, 50)
    && minStringLengthValidation(name, 3)
);
```

---

## Creating generic validators (1/3)
You can create a generic function to check multiple validations

```ts
export const isValid = (...validators): any =>
  (arg: any) => isNil(find(val => !val(arg), validators));
```

---

## Creating generic validators (2/3)
Then create small validation functions

```ts
export const maxNumberValidation = (max: number) =>
  (value: number) => value < max;
export const maxStringLengthValidation = (max: number) =>
  (value: string) => value.length < max;
export const minStringLengthValidation = (min: number) =>
  (value: string) => value.length > min;
```

---

## Creating generic validation (3/3)
Then pipe your validation rules into `isValid`

```ts
export const createFormFieldSelector = (fieldPath: string[]) => createSelector(
  formStateSelector,
  (form: IForm) => path(fieldPath, form)
);

export const isNameValidSelector = createSelector(
  createFormFieldSelector(['character', 'name']),
  isValid(
    maxStringLengthValidation(50),
    minStringLengthValidation(3),
  ),
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
