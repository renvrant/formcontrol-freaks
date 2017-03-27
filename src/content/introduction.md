# FormControl Freaks: Redux Edition

# Rangle.io
### Daniel Figueiredo & Renee Vrantsidis

---

## What's a Rangle?
- We build Angular (and other JS) apps
- We use Redux/NgRx for state management
- We work with a lots of clients, and we noticed a pattern...

---

## Redux can level up your forms

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
- Framework-agnostic validation with selectors

---

![Redux Flow](content/images/full-redux-flow.png "Redux Flow")

---

## Not in this talk
- Intro to Redux

---

## We'll be using...
- Redux & angular-redux (but NgRx will work)
- Ramda (utilities for our reducer)
- Reselect (for selectors)
