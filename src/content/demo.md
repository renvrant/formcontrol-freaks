## Project Dependencies
- Redux
- NgRedux
- Reselect
- Ramda

---

## Create form in the store
Your form will be an object on the store, shaped however you like

```ts
export interface ICharacter {
  name?: string;
  bioSummary: IBioSummary;
  skills: TSkills;
}

export interface IBioSummary {
  age: number;
  size: string;
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
        saveForm({
          value: change,
          path: ['character']
        })
      )
    );
}

ngOnDestroy() {
  this.formSubs.unsubscribe();
}
```

---

## Add another form



---

## Multi-entry fields


```ts
case 'ADD_SKILL':
    return R.assocPath(
      ['character', 'skills'],
      R.concat(state.character.skills, ['']),
      state
    );
  case 'REMOVE_SKILL':
    return R.assocPath(
      ['character', 'skills'],
      R.remove(
        action.payload,
        1,
        state.character.skills
      ),
      state
    );
  case 'SELECT_SKILL':
    return R.assocPath(
      ['character', 'skills'],
      R.update(
        action.payload.index,
        action.payload.skill,
        state.character.skills
      ),
      state
    );
```

---

## Adding actions

The payload for these actions will be an index as well as the new formdata rather than what we saw for `SAVE_FORM`

```ts
export const addSkill = () => ({ type: 'ADD_SKILL' });

export const selectSkill = (skill, index) => ({
  type: 'SELECT_SKILL',
  payload: {
    skill,
    index
  }
});

export const removeSkill = index => ({
  type: 'REMOVE_SKILL',
  payload: index
});
```

---

## On the form

```ts
<label>Skills:</label>
<div *ngFor="let cs of characterForm.skills; let i = index;">
  <select
    [value]="cs"
    (change)="onSelectSkill($event, i)">
    <option *ngFor="let skill of skills" [value]="skill">
      {{ skill }}
    </option>
  </select>
  <button type="button" (click)="removeSkill(i)">Remove</button>
</div>
<button type="button" (click)="addSkill()">Add skill</button>
```

---

## Adding validation with reselect

- Reselect is a library to be used with redux
- Selectors can compute data from the store
- We can use reselect to computer the validity of our form data from the store, then tell our components about it

### Benefits
- Doing validation through selectors can give you performance improvements in some cases
- Isolate all the validators to redux also makes these forms more portable if you want to put them in NativeScript for instance 
- Add benefits of this approach... computing values, not changing the state all the time. can prioritize computation as opposed to changes in the store

---

## Setting up a simple selector 

Set up a selector to check for presence

```ts
import {IAppState} from '../store/store';
import {createSelector} from 'reselect';
import {IForm} from '../store/form/types';

const formStateSelector = (state: IAppState) => state.form;

const characterFormSelector = createSelector(
  formStateSelector,
  (form: IForm) => form.character
);

export const isFormValid = createSelector(
  characterFormSelector,
  character => character.name
    && character.bioSummary.race
    && character.bioSummary.alignment
    && character.skills.length > 0
);
```

---

## Add to our component

```ts
import {isFormValid} from '../selectors/character';
```

Use select to subscribe to 

```ts
  @select(isFormValid) isFormValid$;
```

```ts
<button 
  [disabled]="!(isFormValid$ | async)"
  type="submit">
  Save
</button>
```

---

## Using generic validators


---

## Adding asynchronous validation


---

## Conclusion

 This approach allows you to do anything because it's just JavaScript (or Typescript :P)
 be creative, do what you need to do
