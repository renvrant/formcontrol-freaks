# FormControl Freaks: Redux Edition

# Rangle.io
### Daniel Figueiredo & Renee Vrantsidis

---

## Enterprises are adopting Angular.

---

## Enterprises love forms.

---

## The problem

- Enterprises often require many long forms
- These forms sometimes have weird rules
- They cannot be template-driven, creating a lot of boilerplate
- If you're using Redux, integrating can be tricky

---

## Redux can power up your forms.

---

## Why back forms with Redux?
- Moves your form setup out of the component
- Centralizes form data, easier abstraction on common operations
- Declarative and template-driven forms (with validation!)
- UX enhancements - form data in local storage
- Pure JS functions are testable, typable and extensible

---

## In this talk
How to create Redux-backed forms:
- Creating forms in the store
- Binding forms to state
- Multi-entry fields
- Validation

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
- Validation
- Optimization

---

## Project Dependencies
- [Redux](https://github.com/reactjs/redux)
- [@angular-redux/store](https://github.com/angular-redux/store)
- [Reselect](https://github.com/reactjs/reselect)
- [Ramda](ramdajs.com)
- [RXJS](https://github.com/Reactive-Extensions/RxJS)
