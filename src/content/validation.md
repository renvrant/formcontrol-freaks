# Adding validation

---

## Validation as selectors
- Selectors are middleware that can compute data from the store
- We don't have to track validity explicitly in the store - we can compute it, and update the store less
- We can use reselect to compute the validity of our form data from the store, then tell our components about it

---

## Creating a character selector
Selectors chain functions and pipe the return value of one function into the next. 
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
For now let's just check for presence

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
export const isAgeValidSelector = createSelector(
  bioSummarySelector,
  (bioSummary: IBioSummary) => {
    if (!bioSummary.race) {
      return false;
    }
    switch (bioSummary.race) {
    case 'Human':
      return gte(bioSummary.age, 14) && lte(bioSummary.age, 40);
    case 'Elf':
      return gte(bioSummary.age, 80) && lte(bioSummary.age, 800);
    case 'Tiefling':
      return gte(bioSummary.age, 35) && lte(bioSummary.age, 53);
    }
  }
);
```

---

## Creating generic validators (2/3)
You can create a generic validator function to be used with reselect

```ts
export const isValid = (...validators): any => {
  return (arg: any) => {
    for (const val of validators) {
      if (!val(arg)) {
        return false;
      }
    }
    return true;
  };
};
```

---

## Creating generic validators (2/3)
Then create validation functions

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

## Generic validation alternative
Or, if you're not comfortable with closures...

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
