
# Extending your existing form and form reducer

---

## What's a multi-entry field?
Fields whose values will be stored as an array

```ts
character: {
  skills: ['Climb', 'Knowledge Arcana'],
}
```

---

## Adding new actions
Add actions to support repeatable `select` fields

```ts
export const putInArray = (path, value, index) => ({
  type: 'UPDATE_INDEXED_FORM_VALUE',
  payload: { path, value, index }
});

export const addIntoArray = (path, value) => ({
  type: 'ADD_INDEXED_FORM_VALUE',
  payload: { path, value }
});

export const removeFromArray = (index, path) => ({
  type: 'REMOVE_INDEXED_FORM_VALUE',
  payload: { index, path }
});
```

---

## Updating the reducer (1/2)

```ts
import { concat, remove, update } from 'ramda';

case 'UPDATE_INDEXED_FORM_VALUE':
  const lensForProp = lensPath(action.payload.path);
  const propValue = <any[]> view(lensForProp, state);
  return assocPath(
    action.payload.path,
    update(action.payload.index, action.payload.value, propValue),
    state
  );
```

---

## Updating the reducer (2/2)

```ts
case 'ADD_INDEXED_FORM_VALUE':
  const lensForProp = lensPath(action.payload.path);
  const propValue = <any[]> view(lensForProp, state);
  return assocPath(
    action.payload.path,
    concat(propValue, [action.payload.value]),
    state
  );

case 'REMOVE_INDEXED_FORM_VALUE':
  const lensForProp = lensPath(action.payload.path);
  const propValue = <any[]> view(lensForProp, state);
  return assocPath(
    action.payload.path,
    remove(action.payload.index, 1, propValue),
    state
  );
```

---

## Update our form component (1/2)
These new actions let us add a list of skills

```ts
onSelectSkill({event, index}) {
  const skill = event.target.value;
  this.ngRedux.dispatch(putInArray({
    value: skill,
    index,
    path: [ 'character', 'skills' ]
  }));
}
```

---
## Update our form component (2/2)

```ts
addSkill() {
  this.ngRedux.dispatch(addIntoArray({
    value: undefined,
    path: [ 'character', 'skills' ]
  }));
}

removeSkill(index) {
  this.ngRedux.dispatch(removeFromArray({
    index,
    path: [ 'character', 'skills' ]
  }));
}
```

---

## Update our form template
Using template directives, we create a repeating `select`

```html
<label>Skills:</label>

<div *ngFor="let skillSlot of characterForm.skills; let i = index;">
  <select
    [value]="skillSlot"
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

## The completed form will...
- Add an empty item to the array when `addSkill` is called, showing the user another `select`
- Dispatch `putInArray` when the value of the select changes, updating the skill in place
- Remove the skill at the appropriate index when `removeSkill` is called, removing the `select` from the UI as well 

