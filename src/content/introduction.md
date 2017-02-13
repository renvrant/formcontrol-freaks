# FormControl Freaks: Redux Edition


# Rangle.io
### Daniel Figueiredo & Renee Vrantsidis

---

## Cats

- Here are pictures of our cats so you'll like us.
- Awwwww.

---

Enterprises love forms.

---

## The problem

- Many large forms means verbose, boilerplate code
- Maintenance, API changes are hard to deal with
- Template driven forms are nicer but you can't use them with validation
- Integrating with Redux can be tricky

---

## Managing complexity with Redux

- Redux simplifies how we manage application state
- All state information is centralized in the store

---

Redux can improve forms too.

---

## Why is this approach good?
- Makes forms more declarative
- Centralizes form data
- Template-driven forms with validation
- UX enhancements from Redux
- Pure JS functions for business rules

---

Hold on, doesn't this just move the boilerplate somewhere else?

---

## Why isn't this approach stupid?
- On large-scale projects, the benefit will outweigh the cost
- A single reducer can be used for many forms
- Generic actions and validators can be re-used
- Set it up once, use it many times

---

## In this talk
- Creating forms in the store
- Binding forms to state
- Multi-entry fields
- Validaton
- Optimization

---

## Not in this talk
- Redux 101
- Adding Redux to your application
