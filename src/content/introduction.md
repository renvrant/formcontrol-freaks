# FormControl Freaks: Redux Edition

# Rangle.io
### Daniel Figueiredo & Renee Vrantsidis

---

## Cats

- Here are pictures of our cats so you'll like us.
- Awwwww.

---

## Enterprises love forms.

---

## The problem

- Enterprises often require many long forms
- These forms sometimes have weird rules
- They cannot be template-driven
- If you already use redux, integrating can be tricky

---

## Redux can help.

---

## Why is this approach good?
- Separation of concerns taking advantage of ngForm
- Centralizes form data, easier abstraction on common operations
- Declarative and template-driven forms
- Framework agnostic validations
- UX enhancements - form data on local storage
- Pure JS functions are testable and extensible
- Better typings 

---

## Doesn't this just move the problem somewhere else?

---

## Why isn't this approach stupid?
- On large-scale projects, the benefit will outweigh the cost
- A single parent reducer manages generic form operations
- Computed validations on selectors
- Set it up once, use it many times (even in other frameworks like NativeScript!)

---

### To demonstrate, we needed an example app with lots of forms...

---

## Wizards Wizard
A wizard to help you fill out a Dungeons & Dragons character sheet for your wizard.

[ Live Link here with small walkthrough ]
https://github.com/danielfigueiredo/wizards-wizard

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

---

## Project Dependencies
- Redux
- NgRedux
- Reselect
- Ramda
- RXJS
