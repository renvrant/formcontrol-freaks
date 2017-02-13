# Adding validation

---

## Validation as selectors
- Selectors are middleware that can compute data from the store
- We can use reselect to computer the validity of our form data from the store, then tell our components about it
- computing values, not changing the state all the time. can prioritize computation as opposed to changes in the store

---

## Creating a character selector

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
For now, we'll just validate that the fields are present

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

## Creating generic validators

TODO: Generic validators code snippets
Show the creation of a generic validator (e.g. "is a number")

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

## Selecting multiple validators

TODO: Show how you can chain a generic with a specific validator to compute any given field's validity

---

## Updating `isFormValid`

TODO: Show the modified computation of form validity

---

## Adding validation errors
We still have access to Angular's FormControl states and we can us them to show error messages

```ts
<input
  id="age"
  type="number"
  name="age"
  #ageModel="ngModel"
  [(ngModel)]="characterForm.bioSummary.age">
<div
  [hidden]="isAgeValid || ageModel.control.pristine">
  That age doesn't seem likely! Are you sure it's appropriate for the race you picked?
</div>
```

---

## Adding asynchronous validation

TODO: Async validation
