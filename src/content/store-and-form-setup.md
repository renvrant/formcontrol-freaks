# Setting up your form reducer and component 

---

## One reducer to rule them all
We want a single form reducer to handle all of our forms
(and in the darkness bind them)

```ts
export const rootReducer = combineReducers<IAppState>({
  form: formReducer,
});
```

---

## Plan your structure
Your form can be structured however you want on the store

```ts
// The interface our reducer will use
export interface IForm {
  character: ICharacter;
}

export interface ICharacter {
  name?: string;
  bioSummary: IBioSummary;
  skills: string[];
}

export interface IBioSummary {
  age: number;
  alignment: string;
  race: string;
}
```

---

## Reduce, reuse...

Plan any other forms you want to add to your form state

```ts
export interface IForm {
  character: ICharacter; // state.form.character
  equipment: IEquipment; // state.form.equipment
}

export interface IEquipment {
  weaponName?: string;
  weaponType: string;
  armorType: string;
}
```

---

## Setting up actions

Action payloads include the path to the form in the store

```ts
export const saveForm = (path, value) => ({
  type: 'SAVE_FORM',
  payload: {
    path, // ['character'] would be form.character
    value,
  }
});
```

---

## Create form reducer
Our form changes will be merged at the provided path

```ts
import { lensPath, assocPath, merge } from 'ramda';

export function formReducer(state = initialState: IForm, action) {
  switch (action.type) {
  case 'SAVE_FORM':
    const lensForProp = lensPath(action.payload.path);
    return assocPath(
      action.payload.path,
      merge(view(lensForProp, state), action.payload.value),
      state
    );
  default:
    return state;
  }
}
```

---

## Create form template
In `character-form.html`...

```html
<form #form="ngForm">
  <label for="name">Character Name:</label>
  <input
    type="text"
    name="name"
    [(ngModel)]="characterForm.name">

  <label for="age">Age:</label>
  <input
    type="number"
    name="age"
    [(ngModel)]="characterForm.bioSummary.age">
</form>
```

---

## Create form component
In `character-form.ts`... 

```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';
import { NgRedux, select } from 'angular-redux';
import { saveForm } from '../actions/index';
import { IAppState } from '../store/store';

@Component({
  selector: 'character-form',
  template: require('./character-form.html')
})
export class CharacterForm {
  @ViewChild(NgForm) ngForm: NgForm;
  public characterForm;
  private formSubs;

  constructor(private ngRedux: NgRedux<IAppState>) {}
}
```

---

## Listen and dispatch
`SAVE_FORM` will be dispatched automatically when the form value changes

```ts
ngOnInit() {
  // Dispatch an action when the form is changed
  this.ngForm.valueChanges.debounceTime(0)
    .subscribe(formValues => this.ngRedux.dispatch(
      saveForm(
        ['character'], // path on the store
        formValues,
      )
    ));
  // Subscribe to the form in state
  this.formSubs = this.ngRedux.select(state => state.form.character)
    .subscribe(characterFormState => {
      this.characterForm = characterFormState;
    });
}
```

---

![Redux Flow](content/images/store-component-flow.png "Redux Flow")

---

![Alternative Diagram](content/images/store-component-flow-lolz.png "Redux Flow")
