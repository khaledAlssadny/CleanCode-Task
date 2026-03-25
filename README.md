# Registration Form — Clean Code Refactor

A simple HTML registration form refactored to apply the **10 Clean Code Principles**.  
The project contains two versions of the same form so you can see the before/after difference clearly.

---

## Files

| File | Description |
|------|-------------|
| `form-old.html` | Original code — validation logic crammed into one function |
| `form-new.html` | Refactored code — 10 Clean Code Principles applied |

---

## What Changed & Why

### 1. Meaningful Names
**Old:** logic lived inside one generic `validateForm()` with no descriptive sub-names.  
**New:** each concern gets its own clearly named function.
```js
// Old — unclear what "checking" happens here
function validateForm() { ... }

// New — name tells you exactly what it does
function validateName(name) { ... }
function validateEmail(email) { ... }
```

---

### 2. Single Responsibility Principle (SRP)
**Old:** `validateForm()` did everything — read inputs, validate, set errors, show success.  
**New:** each function has one job and does nothing else.
```js
getInputValue(id)   // only reads & trims input
setError(id, msg)   // only updates the DOM
clearErrors()       // only clears previous errors
validateName(name)  // only validates the name rule
```

---

### 3. DRY — Don't Repeat Yourself
**Old:** `document.getElementById(id).value.trim()` and `document.getElementById(id).textContent = ...` were copy-pasted for every field.  
**New:** extracted into reusable helpers called once per field.
```js
function getInputValue(id) {
  return document.getElementById(id).value.trim();
}

function setError(id, message) {
  document.getElementById(id).textContent = message;
}
```

---

### 4. Small Functions
**Old:** one massive function (~40 lines) handling all four fields.  
**New:** every function is 3–5 lines and fits on one screen.

---

### 5. Replace Magic Numbers with Named Constants
**Old:** raw numbers scattered through the logic with no explanation.
```js
// Old — why 2? why 6? why 120?
if (name.length < 2) { ... }
if (password.length < 6) { ... }
if (age < 1 || age > 120) { ... }
```
**New:** constants declared at the top with self-documenting names.
```js
const MIN_NAME_LENGTH = 2;
const MIN_PASSWORD_LENGTH = 6;
const MIN_AGE = 1;
const MAX_AGE = 120;
```

---

### 6. Avoid Side Effects in Validators
**Old:** validators directly manipulated the DOM, mixing logic with UI.  
**New:** validators are pure — they only return a string, touching nothing else.
```js
// New — no DOM access, no side effects
function validatePassword(password) {
  if (password === "") return "Password is required.";
  if (password.length < MIN_PASSWORD_LENGTH) return "Password must be at least 6 characters.";
  return "";
}
```

---

### 7. Consistent Abstraction Level
**Old:** high-level orchestration mixed with low-level DOM calls in one place.  
**New:** `validateForm()` only orchestrates; DOM work is delegated to helpers.
```js
function validateForm() {
  clearErrors();                          // ← high level
  const name = getInputValue("name");     // ← high level
  const nameError = validateName(name);   // ← high level
  setError("nameError", nameError);       // ← high level
}
```

---

### 8. Proper Error Handling
**Old:** errors set inside each `if/else` block with no central clearing step.  
**New:** errors are cleared first, then set only when a rule fails, and success only shows when all fields pass.
```js
clearErrors();
// ... run all validators ...
const isValid = !nameError && !emailError && !passwordError && !ageError;
if (isValid) {
  setError("successMsg", "✅ Form submitted successfully!");
}
```

---

### 9. Separation of Concerns
**Old:** validation logic, DOM reading, and UI updates all tangled together.  
**New:** three layers are clearly separated.

- **Data layer** → `getInputValue()` reads the form
- **Logic layer** → `validateName()`, `validateEmail()`, etc. check the rules  
- **UI layer** → `setError()`, `clearErrors()` update what the user sees

---

### 10. Readable Top-Down Flow
**Old:** you had to trace through nested `if/else` blocks to understand the flow.  
**New:** `validateForm()` reads like plain English from top to bottom.
```js
function validateForm() {
  clearErrors();

  const name = getInputValue("name");
  const nameError = validateName(name);
  setError("nameError", nameError);

  // ... same pattern for each field ...

  if (isValid) {
    setError("successMsg", "✅ Form submitted successfully!");
  }
}
```

---

## Summary Table

| # | Principle | Demonstrated By |
|---|-----------|-----------------|
| 1 | Meaningful Names | `validateEmail`, `getInputValue`, `clearErrors` |
| 2 | Single Responsibility | Each function does exactly one thing |
| 3 | DRY | `getInputValue` and `setError` replace repeated code |
| 4 | Small Functions | Every function is under 6 lines |
| 5 | No Magic Numbers | `MIN_NAME_LENGTH`, `MAX_AGE`, etc. |
| 6 | No Side Effects | Validators return strings, never touch the DOM |
| 7 | Consistent Abstraction | `validateForm` stays high-level throughout |
| 8 | Error Handling | Clear-then-set pattern, success only on full pass |
| 9 | Separation of Concerns | Data / Logic / UI layers are distinct |
| 10 | Readable Flow | `validateForm` reads top-to-bottom like a checklist |

---

## How to Run

No build tools needed. Just open either file in your browser.

```bash
open form-old.html   # before refactor
open form-new.html   # after refactor
```
