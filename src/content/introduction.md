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

## Why is this approach good?
- Moves your form setup out of the component
- Centralizes form data, simplifying things for other parts of the app that need it
- Declarative, template-driven forms, but with validation
- UX enhancements - users can leave and come back with state rehydration
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
