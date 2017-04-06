# FormControl Freaks: Redux Edition

# Rangle.io
### Daniel Figueiredo & Renee Vrantsidis

---

![Wizard Cat](content/images/wizardcat.jpg "Wizard Cat")

---

## Redux can make your forms magical

---

## (And you probably have a lot of forms)

---

## Why?
- Lower maintenance cost on large-scale apps from moving form setup out of components
- Centralizes form data, easier abstraction on common operations
- Declarative and template-driven forms (with validation!)
- Pure JS functions are testable, typable and extensible

---

### To demonstrate, we needed an example app with lots of forms...

---

## In this talk
- Your forms + Redux magic
- Adding form data to state
- Supporting multi-entry fields
- Controlling validation with selectors

---

## Not in this talk
- Redux 101

---

![Redux Flow](content/images/full-redux-flow.png "Redux Flow")

---

## We'll be using...
- Redux & angular-redux (but NgRx will work)
- Ramda (utilities for our reducer)
- Reselect (for selectors)
