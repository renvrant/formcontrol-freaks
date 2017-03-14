# Setting up your form reducer and component 

---

## Plan the shape of your form 
You can structure your store however you like
`form.character` will represent a character form in this example

```ts
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

## Setting up actions
Action payloads include the path to the form in the store

```ts
export const saveForm = (path, value) => ({
  type: 'SAVE_FORM',
  payload: {
    path,
    value
  }
});
```

---

## Create form reducer
Including a path in the payload makes this reducer reusable for other forms

```ts
import { lensPath, assocPath, merge } from 'ramda';
import { initialState } from './initial-state';

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

## Create form component

```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';
import { NgRedux, select } from 'ng2-redux';
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

## Create form template
Use a template driven form and bind your inputs to object retrieved from state

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

## Listen and dispatch

`SAVE_FORM` will be dispatched automatically when the form is changed

```ts
ngOnInit() {
  // Subscribe to the form in state
  this.formSubs = this.ngRedux.select(state => state.form.character)
    .subscribe(characterFormState => {
      this.characterForm = characterFormState;
    });
  // Dispatch an action when the form is changed
  this.ngForm.valueChanges.debounceTime(0)
    .subscribe(change =>
      this.ngRedux.dispatch(
        saveForm(
          change,
          ['character']
        )
      )
    );
}
```

---

### Our form is now writing to state!

---

## Reduce, reuse...
Other forms can use the same reducer
Both `form.character` and `form.equipment` will use the `formReducer`

```ts
export interface IEquipment {
  weaponName?: string;
  weaponType: string;
  armorType: string;
}

export interface IForm {
  character: ICharacter;
  equipment: IEquipment;
}
```
