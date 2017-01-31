## Add Redux and Configure Your Store

- Your form will be an object on the store
- Your form reducer will need an action to save the form and update the state
- Your interfaces will represent what you want your form to look like on the store

---

## Starter Form Reducer

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

## Create the Store

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

## Add a Save Form Action

We want an action that will be dispatched to write the form to state.

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

Use two-way data binding

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

## See your fields are pushed automatically to the store

---

## Add fieldsets to group data

---

## Add validation using selectors

---
