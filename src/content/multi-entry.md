
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
export const addIntoArray = (path, value) => ({
  type: 'ADD_MULTI_ENTRY_FORM_VALUE',
  payload: { path, value }
});

export const putInArray = (path, value, index) => ({
  type: 'UPDATE_MULTI_ENTRY_FORM_VALUE',
  payload: { path, value, index }
});

export const removeFromArray = (path, index) => ({
  type: 'REMOVE_MULTI_ENTRY_FORM_VALUE',
  payload: { path, index }
});
```

---

## Updating the reducer

```ts
import { assocPath, path, update } from 'ramda';

case 'UPDATE_MULTI_ENTRY_FORM_VALUE':
  const propPath = action.payload.path;
  return assocPath(
    propPath,
    update(
      action.payload.index, 
      action.payload.value, 
      path<string[]>(propPath, state)
    ),
    state
  );
```

---

## Update our form component
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

