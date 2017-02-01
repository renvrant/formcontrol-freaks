## Add Redux and Configure Your Store

- Your form will be an object on the store
- Your form reducer will need an action to save the form and update the state
- Your interfaces will represent what you want your form to look like on the store

---

## Create the Store

First you need to add Redux to your application, expecting a `formReducer`

```ts
import {
  createStore,
  combineReducers,
} from 'redux';
import { formReducer } from './form';

export interface IAppState {
  form?: IForm;
};

const rootReducer = combineReducers<IAppState>({
  form: formReducer,
});

export const store = createStore(rootReducer, {});
```

---

## Starter Form Reducer

- A reducer with a `SAVE_FORM` action
- Expects an object representing the form and merges the whole thing with the current state
- Optimizations to come later

```ts
import {
  ICharacter,
  IForm
} from './types';
import {IPayloadAction} from '../../actions/index';
import * as R from 'ramda';

const initialState: IForm = {
  character: ICharacter = {};
};

export function formReducer(state = initialState, action: IPayloadAction) {
  switch (action.type) {
  case 'SAVE_FORM':
    return R.assoc(
      'character',
      R.merge(state.character, action.payload),
      state
    );
  }
}
```

---

## Add a Save Form Action

We also need a `SAVE_FORM` action to dispatch with the form as a payload

```ts
import {Action} from 'redux';

export interface IPayloadAction extends Action {
  payload?: any;
}

export const saveForm = (form) => ({
  type: 'SAVE_FORM',
  payload: form
});
```

---

## Create our Form Component

We'll need Redux, our app state, our action, and NgForm to get started on our form component. `characterForm` will represent our form as it exists in state

```ts
import { Component } from '@angular/core';
import { NgRedux, select } from 'ng2-redux';
import { saveForm } from '../actions/index';
import {IAppState} from '../store/store';
import {NgForm} from '@angular/forms';

@Component({
  selector: 'rio-form',
  template: `<form #form="ngForm"></form>`
})
export class RioForm {
  @ViewChild(NgForm) ngForm: NgForm;
  characterForm;
  private formSubs;

  constructor(private ngRedux: NgRedux<IAppState>) {}
}
```

---

## Add a Subscription to the Store

We want to listen to value changes on the form and dispatch an action to update the state.

We also want to subscribe to state to keep our private `characterForm` in sync with its values.

```ts
ngOnInit() {
  this.formSubs = this.ngRedux.select(state => state.form.character)
    .subscribe(characterForm => {
      this.characterForm = characterForm;
    });
  this.ngForm.valueChanges.debounceTime(0).subscribe(change => this.ngRedux.dispatch(saveForm(change)));
}

ngOnDestroy() {
  this.formSubs.unsubscribe();
}
```

---

## Add form field 

We use two-way binding on inputs to bind to `characterForm`

```ts
<form (ngSubmit)="onSubmit(form.value)" #form="ngForm">
  <label for="name">Character Name:</label>
  <input
    type="text"
    name="name"
    #characterModel="ngModel"
    [(ngModel)]="characterForm.name">
</form>
```

---

## Add fieldsets to group data

Our form object doesn't need to be flat.

```ts
export interface ICharacter {
  name?: string;
  bioSummary: {
    race: string;
    alignment: string;
};
```

---

## Add fieldsets to group data

Specify the correct path when binding the input to `ngModel`

```ts
<fieldset ngModelGroup="bioSummary">
  <label for="race">Race:</label>
  <input
    name="race"
    [(ngModel)]="characterForm.bioSummary.race">
  <label for="alignment">Alignment:</label>
  <select
    name="alignment"
    [(ngModel)]="characterForm.bioSummary.alignment">
    <option [value]="Good">Good</option>
    <option [value]="Neutral">Neutral</option>
    <option [value]="Evil">Evil</option>
  </select>
</fieldset>
```

---

## Expanding our reducer

We can use generic actions for handling most of the form. Let's add another action that handles multi-entry form fields

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
