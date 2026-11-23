# Functions With Parameters

[![Tests](https://github.com/RickRieger/functions-with-parameters/actions/workflows/tests.yml/badge.svg)](https://github.com/RickRieger/functions-with-parameters/actions/workflows/tests.yml)

A JavaScript exercise from the **Code Immersives** web development bootcamp in New York City (February 2021). It's the follow-up to [Functions Ahoy](https://github.com/RickRieger/functions-ahoy): the functions now take **parameters**, values passed in when the function is called, and use them to change global variables. [Jest](https://jestjs.io/) tests call each function with sample values and check the result. This repo is my fork of the assignment. My solution is at the top of [`main.test.js`](main.test.js), where this assignment puts the code.

**Skills practiced:** function parameters, the modulus operator (`%`), negating a number, building strings, and noticing when the instructions and the tests disagree.

**Built with:** JavaScript (Node.js) and Jest 24. There's no web page; the badge above shows whether the tests currently pass on GitHub.

---

## Contents

- [My solution](#my-solution)
- [The makeFullName puzzle](#the-makefullname-puzzle)
- [Run the tests](#run-the-tests)
- [How it's verified](#how-its-verified)
- [Cleanup log (November 2026)](#cleanup-log-november-2026)
- [Original assignment (from the instructor)](#original-assignment-from-the-instructor)

---

## My solution

Finished on **2021-02-17**. All 5 of the assignment's tests passed. *(Shown as it is after the 2026 tidy-up; the logic is unchanged.)*

```js
let x = 3;
let y = 10;
let z = 5;
let name = 'Rick';
let greeting = 'HEY';

function multiply(num) {
  x = num * x;                               // multiply(15) then multiply(3): 3 → 45 → 135
}

function modYBy(divisor) {
  y = y % divisor;                           // modYBy(4): 10 % 4 → 2
}

function opposite(value) {
  z = -value;                                // opposite(3) → -3, opposite(-85) → 85
}

function makeFullName(lastName) {
  name = name + ' ' + lastName;              // makeFullName('Jaffe'): 'Rick' → 'Rick Jaffe'
}

function yellAt(person) {
  greeting = greeting + ', ' + person + '!'; // yellAt('Colin'): 'HEY' → 'HEY, Colin!'
}
```

| Function | What it shows |
|---|---|
| `multiply(num)` | A **parameter** is a placeholder for whatever value gets passed in. The test calls `multiply(15)` and then `multiply(3)`. Each call updates `x`, so the effects stack: 3 × 15 = 45, then 45 × 3 = 135. |
| `modYBy(divisor)` | The **modulus operator** `%` gives the remainder of a division: 10 ÷ 4 = 2 remainder **2**. |
| `opposite(value)` | Putting a minus sign in front of a value flips its sign, and it works both ways: `-(3)` is `-3` and `-(-85)` is `85`. It ignores the old value of `z`, as the instructions ask. |
| `makeFullName(lastName)` | Adds a space and the passed-in string to the end of `name`. |
| `yellAt(person)` | Builds `"HEY, Colin!"` by joining the old greeting, a comma and space, the name and an exclamation point. |

## The makeFullName puzzle

The instructions say *"Create a **variable** called `makeFullName`…"*, but the test runs `makeFullName('Jaffe')`. You can only call a function that way, not a variable holding a string. My commit history shows me working it out:

1. **First try** (commit *"Something is strange about the makeFullName task"*): followed the wording literally.
   ```js
   let makeFullName = name + ' Rieger';   // a string, so makeFullName('Jaffe') fails
   ```
2. **Second try** (commit *"git er dun"*): made it a function, but ignored the parameter and hard-coded the name the test uses.
   ```js
   function makeFullName(oldName){
     return name = name + ' ' + 'Jaffe';  // passes the test, but only works for "Jaffe"
   }
   ```
3. **Final answer** (commit *"update to JS"*): uses the parameter, so it works for any last name.
   ```js
   function makeFullName(lastName){
     name = name + ' ' + lastName;
   }
   ```

**Takeaway:** when the instructions and the tests disagree, the tests show what the code actually has to do. Passing a test isn't the same as being correct; version 2 passed but would break for any other name.

## Run the tests

You need [Node.js](https://nodejs.org/).

```bash
git clone https://github.com/RickRieger/functions-with-parameters.git
cd functions-with-parameters
npm ci
npm run test:ci   # runs all tests once and exits
npm test          # watch mode, as in the original assignment: re-runs on every save
```

Expected result: `Tests: 6 passed, 6 total` (the 5 original tests plus 1 added in the cleanup below).

## How it's verified

[`.github/workflows/tests.yml`](.github/workflows/tests.yml) runs `npm run test:ci` on GitHub Actions after every push, and the badge at the top shows the result. This repo has no web page, so instead of a live site, a passing test run is how it shows it works.

---

## Cleanup log (November 2026)

All changes below were made on **2026-11-23** on the branch `cleanup-and-ci`, checked by running the tests, and merged into `master`. Each is its own commit. Nothing below the assignment's "DON'T CHANGE ANYTHING BELOW HERE" line was edited. The solution worked correctly before the cleanup, so these are clarity and testing improvements.

### 1. Renamed parameters so `yellAt` no longer hides the global `name`

**Why:** `yellAt`'s parameter was called `name`, the same as the global variable holding my first name. Inside the function, `name` meant the parameter, so the global was *shadowed* (hidden). It worked, but it's easy to misread, and the function couldn't reach the global `name` if it ever needed to. `num2` and `num3` didn't say what they were for either.

```js
// Before
function modYBy(num2){ y =  y % num2; }
function opposite(num3) { z = -num3; }
function yellAt(name){ greeting = greeting + ','+' '+name + '!'; }

// After
function modYBy(divisor) { y = y % divisor; }
function opposite(value) { z = -value; }
function yellAt(person) { greeting = greeting + ', ' + person + '!'; }
```

### 2. Tidied the code section and replaced the "something is flawed" note

**Why:** the indentation and spacing were uneven (`yellAt`'s body wasn't indented, and `','+' '+name` was hard to read). The comment `//* There must be something flawed with the code/test here` was left over from the `makeFullName` confusion. It's now a note that explains the issue.

```js
// Before
//* There must be something flawed with the code/test here


function makeFullName(lastName){
 name = name + ' ' + lastName;
}

// After
// Note: the instructions call makeFullName a "variable", but the test calls
// it like a function (makeFullName('Jaffe')), so it has to be a function.
function makeFullName(lastName) {
  name = name + ' ' + lastName;
}
```

### 3. Added a test for `opposite()` with a negative number

**Why:** the instructions give two examples, `6 → -6` and `-85 → 85`, but the original test only tries `3`. The new test checks the negative case. My solution already handled it.

```js
describe('opposite (negative input)', () => {
  it('turns a negative number positive', () => {
    opposite(-85);
    expect(z).toBe(85);
  });
});
```

### 4. Automatic test runs on GitHub, and a `.gitignore` fix

**Why:** `npm test` runs Jest in *watch mode*, which never exits, so an automated check couldn't use it. I added a run-once command and a GitHub Actions workflow. `.gitignore` also listed `node_modules` twice.

```jsonc
// Before (package.json)
"scripts": { "test": "jest --watch-all" }

// After
"scripts": {
  "test": "jest --watch-all",   // unchanged: watch mode while working
  "test:ci": "jest --ci"        // new: run once and exit
}
```

### 5. Rewrote this README section

Added everything above. The instructor's original instructions are below, unchanged. Note that their title, "Functions With Global Variables", is carried over from the previous assignment.

---

## Original assignment (from the instructor)

*The text below is the assignment README as written by the Code Immersives instructor, unchanged.*

### Functions With Global Variables

### Setup

1. Navigate to this directory in your terminal.
2. Run `npm install --global jest` to install the testing library (if you've already done this, skip this step!).
3. Preferably in a full-screen terminal, run `jest --watch-all` to start testing.

### Workflow

You'll be working in **`main.test.js`** this time!, creating the variables needed with the values asked for, according to the specifications given in the tests. Check your terminal for feedback on which aspect of the problem you have yet to complete, and read the specifications' actual code implementation for extra help; it makes explicit exactly what outputs are expected given the test inputs.

### Guidelines:

- Please do **not** call the functions; just declare them! You can call them for your own testing purposes, but then either delete or comment out the line.
- [ ] If you are asked to define a variable in terms of another, do not recalculate the value of that variable. For example, if x = 5, and y = 6, and z is supposed to be x + y, do NOT set y to equal 11 directly, or say that y should be 5 + 6. Use the variables instead.

### Tasks

Let's start by creating our variables so that we can manipulate them later on with functions.

- Create a variable called `x` and set it to the value 3.
- Create a variable called `y` and set it to the value 10.
- Create a variable called `z` and set it to the value 5.
- Create a variable called `name` and set it the string version of your first name.
- Create a variable called `greeting` and set it to the value `'HEY'`.

Now for some functions!

- Create a function called `multiply` that takes in one parameter and changes `x` to the product of `x` and that parameter.
- Create a function called `modYBy` that takes in one parameter, divides `y` by that parameter, and sets `y` to the remainder of that division. Look up the JavaScript modulus operator if you don't remember it, because it could sure come in handy here!
- Create a function called `opposite` that takes in a parameter and sets `z` to equal the opposite sign of that parameter. For example, if the function is passed `6`, `z` should be set to `-6`, and if passed `-85`, `z` should be set to `85`. Note that we are not "reading" the value of `z` here. In other words, unlike the other problems here, it doesn't matter what `z` used to be!
- Create a variable called `makeFullName` that takes in a string parameter and sets `name` to have that string at the end, with a space in the middle.
- Create a function called `yellAt` that takes in a string parameter and adds that string to the end of `greeting`, with a comma and a space in between and an exclamation point at the end. This string concatenation (or "smooshing" in the technical parlance) should be set to be the new value of `greeting`.
