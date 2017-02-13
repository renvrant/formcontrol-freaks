# Setting up your form reducer and component 

---

## Create form in the store
Your form will be an object that uses a single reducer

```ts
export interface IAppState {
  form?: IForm;
}

export const rootReducer = combineReducers<IAppState>({
  form: formReducer
});
```

---

## Plan the shape of your form 
You can structure your store however you like
We'll use `form.character` to represent a character form in this example

```ts
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

export interface IForm {
  character: ICharacter;
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
Our reducer is reusble because it merges the new state at the provided path

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

## Create form template
Use a template driven form with inputs double-bound to ngModel. 
Mirror the structure of your form in state

```ts
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

We'll need Redux, our app state, our action, and NgForm to get started on our form component. The `characterForm` object will represent our form and its values.

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
  characterForm;
  private formSubs;

  constructor(private ngRedux: NgRedux<IAppState>) {}
}
```

---

## Listen and dispatch

`SAVE_FORM` will be dispatched automatically when the form is changed, along with the root path

```ts
ngOnInit() {
  // Subscribe to the form in state
  this.formSubs = this.ngRedux.select(state => state.form.character)
    .subscribe(characterForm => {
      this.characterForm = characterForm;
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

ngOnDestroy() {
  this.formSubs.unsubscribe();
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
