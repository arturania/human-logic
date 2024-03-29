# Human Logic or Common Sense

[![Build Status](https://github.com/arturania/human-logic/actions/workflows/build.yml/badge.svg)](https://github.com/arturania/human-logic/actions/workflows/build.yml) [![Coverage Status](https://coveralls.io/repos/github/arturania/human-logic/badge.svg?branch=master)](https://coveralls.io/github/arturania/human-logic?branch=master) [![NPM version](https://img.shields.io/npm/v/human-logic.svg)](https://www.npmjs.com/package/human-logic) [![License](https://img.shields.io/github/license/arturania/human-logic.svg)](https://github.com/arturania/human-logic/blob/master/LICENSE)

Human Logic (also known as “common sense”) is based on five categories:

- `true` = certainly positive
- `false` = certainly negative
- `maybe` = uncertain (could be either positive or negative)
- `never` = impossible (neither positive nor negative)
- `undefined` = totally unknown

This package provides the implementation of both Discrete Common Sense Logic and Fuzzy Common Sense Logic.

Discrete Common Sense Logic only allows `true`, `false`, `maybe`, `never` or `undefined` as a value.

In Fuzzy Common Sense Logic the value is five-dimensional unit vector. Each vector component is a fuzzy value (between 0.0 and 1.0 inclusive) of respective `true`, `false`, `maybe`, `never` or `undefined` category.

## Migration from v1 to v2

* `Category` type was migrated from [numeric enum](https://www.typescriptlang.org/docs/handbook/enums.html#numeric-enums) to `string` [const assertions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions)
* `Category` type values `UNDEF`, `FALSE`, `NEVER`, `MAYBE`, `TRUE` are now strings (not numbers).
* `LogicHash` interface was removed – use `LogicValues` interface instead.
* `Logic.asHash(...)` was removed – use `Logic.asValues(...)` instead.
* `Logic.fromHash(...)` was replaced by new method `Logic.fromValues(...)`.

## Documentation

API Documentation: [https://arturania.dev/human-logic](https://arturania.dev/human-logic/modules.html)

## Installation

With NPM:

```bash
npm install --save human-logic
```

With Yarn:

```bash
yarn add human-logic
```

## Usage

Node v6+ syntax:

```JavaScript
const {
  // Discrete Common Sense Logic
  Categories, UNDEF, FALSE, NEVER, MAYBE, TRUE,
  // Fuzzy Common Sense Logic
  Logic,
  // Polymorphic Functions
  not, and, or, normalize,
  // Bonus: classical fuzzy logic
  Fuzzy, FUZZY_TRUE, FUZZY_FALSE
} = require('human-logic');
```

ES5+ syntax:

```JavaScript
import {
  // Discrete Common Sense Logic
  Categories, UNDEF, FALSE, NEVER, MAYBE, TRUE,
  // Fuzzy Common Sense Logic
  Logic,
  // Polymorphic Functions
  not, and, or, normalize,
  // Bonus: classical fuzzy logic
  Fuzzy, FUZZY_TRUE, FUZZY_FALSE
} from 'human-logic';
```

## Discrete Common Sense Logic

### Math Background

#### NOT

| `undef` | `false` | `never` | `maybe` | `true` |
| --- | --- | --- | --- | --- |
| `undef` | `true` | `maybe` | `never` | `false` |

#### AND

|  | `undef` | `false` | `never` | `maybe` | `true` |
| --- | --- | --- | --- | --- | --- |
| **`undef`** | `undef` | `undef` | `undef` | `undef` | `undef` |
| **`false`** | `undef` | `false` | `false` | `false` | `false` |
| **`never`** | `undef` | `false` | `never` | `false` | `never` |
| **`maybe`** | `undef` | `false` | `false` | `maybe` | `maybe` |
| **`true`** | `undef` | `false` | `never` | `maybe` | `true` |

#### OR

|  | `undef` | `false` | `never` | `maybe` | `true` |
| --- | --- | --- | --- | --- | --- |
| **`undef`** | `undef` | `undef` | `undef` | `undef` | `undef` |
| **`false`** | `undef` | `false` | `never` | `maybe` | `true` |
| **`never`** | `undef` | `never` | `never` | `true` | `true` |
| **`maybe`** | `undef` | `maybe` | `true` | `maybe` | `true` |
| **`true`** | `undef` | `true` | `true` | `true` | `true` |

### Usage

```JavaScript
not(TRUE)
// => FALSE
and(MAYBE, NEVER)
// => FALSE
or(MAYBE, NEVER)
// => TRUE
Categories
// => [UNDEF, FALSE, NEVER, MAYBE, TRUE]
```

## Fuzzy Common Sense Logic

### Math Background

<p align="center"><img src="/tex/ae77f2859802a1f32c296751f4b4592e.svg?invert_in_darkmode&sanitize=true" align=middle width=156.84716025pt height=118.35736770000001pt/></p>

<p align="center"><img src="/tex/8071c1b11bf036f245308cc614c56f84.svg?invert_in_darkmode&sanitize=true" align=middle width=610.5136719pt height=128.22048195pt/></p>

<p align="center"><img src="/tex/2953f9691864e91672ac7dd328a9f2a0.svg?invert_in_darkmode&sanitize=true" align=middle width=614.52064575pt height=132.16535804999998pt/></p>

where "<img src="/tex/1bf49790e3d1ca92411e3e1573a7d714.svg?invert_in_darkmode&sanitize=true" align=middle width=4.5662248499999905pt height=22.831056599999986pt/>", "<img src="/tex/e7538fc23e29821ea54565f5d88aef0d.svg?invert_in_darkmode&sanitize=true" align=middle width=12.785434199999989pt height=22.831056599999986pt/>" and "<img src="/tex/97b1f92923118406f3fbbf144ddf5198.svg?invert_in_darkmode&sanitize=true" align=middle width=4.5662248499999905pt height=24.65753399999998pt/>" are [classical fuzzy logic](#classical-fuzzy-logic) operations.

### Initialization

```JavaScript
// new instance
const value = new Logic(0.1, 0.2, 0.3, 0.1, 0.4);
// or
const value = Logic.fromValues({
  UNDEF: 0.1,
  FALSE: 0.2,
  NEVER: 0.3,
  MAYBE: 0.1,
  TRUE:  0.4 // — dominating category
});
// or
const value = Logic.fromArray([0.1, 0.2, 0.3, 0.1, 0.4]);

// Result
value.asCategory()
// => TRUE
value.get(NEVER)
// => 0.3
value.isValid() // At least one category fuzzy value is non-zero
// => true
value.eq(TRUE) // Equal to category
// => true
value.ne(MAYBE) // Not equal to category
// => true

const value = Logic.fromCategory(MAYBE);
value.asArray()
// => [0.0, 0.0, 0.0, 1.0, 0.0]
value.asValues()
// => { UNDEF: 0.0, FALSE: 0.0, NEVER: 0.0, MAYBE: 1.0, TRUE: 0.0 }
value.asValues()
// => { [UNDEF]: 0.0, [FALSE]: 0.0, [NEVER]: 0.0, [MAYBE]: 1.0, [TRUE]: 0.0 }

// Cloning
const clonedValue = value.clone();
clonedValue.asValues()
// => { [UNDEF]: 0.0, [FALSE]: 0.0, [NEVER]: 0.0, [MAYBE]: 1.0, [TRUE]: 0.0 }
clonedValue === value
// false

// Normalization
const nonNormalizedValue = Logic.fromValues({
  UNDEF: 2,
  FALSE: 3,
  NEVER: 4,
  MAYBE: 5,
  TRUE:  6
});
const normalizedValue = nonNormalizedValue.normalize();
normalizedValue.asArray()
// => [0.1, 0.15, 0.2, 0.25, 0.3]
nonNormalizedValue.getNormalized(NEVER)
// => 0.2
```

### Logical NOT

```JavaScript
const value = Logic.fromValues({
  UNDEF: 0.10, // 10%
  FALSE: 0.15, // 15%
  NEVER: 0.20, // 20%
  MAYBE: 0.25, // 25%
  TRUE:  0.30  // 30% — dominating category
});

// Use either class method:
value.not().asValues()
// or polymorphic function:
not(value).asValues()
// => {
//   UNDEF: 0.1,  // 10%
//   FALSE: 0.3,  // 30% — dominating category
//   NEVER: 0.25, // 25%
//   MAYBE: 0.2,  // 20%
//   TRUE:  0.15  // 15%
// }
```

### Logical AND

```JavaScript
const value1 = Logic.fromValues({
  UNDEF: 0.15, // 15%
  FALSE: 0.10, // 10%
  NEVER: 0.25, // 25%
  MAYBE: 0.30, // 30% — dominating category
  TRUE:  0.20  // 20%
});
const value2 = Logic.fromValues({
  UNDEF: 0.20, // 20%
  FALSE: 0.30, // 30% — dominating category
  NEVER: 0.10, // 10%
  MAYBE: 0.15, // 15%
  TRUE:  0.25  // 25%
});

// class method
value1.and(value2).asValues()
// polymorphic function
and(value1, value2).asValues()
// => {
//   UNDEF: 0.16666666666666669, // ~17%
//   FALSE: 0.25,                //  25% — dominating category
//   NEVER: 0.20833333333333334, // ~21%
//   MAYBE: 0.20833333333333334, // ~21%
//   TRUE:  0.16666666666666669  // ~17%
// }
```

### Logical OR

```JavaScript
// class method
value1.or(value2).asValues()
// polymorphic function
or(value1, value2).asValues()
// => {
//   UNDEF: 0.18181818181818182, // ~18%
//   FALSE: 0.09090909090909091, //  ~9%
//   NEVER: 0.22727272727272727, // ~23%
//   MAYBE: 0.2727272727272727,  // ~27% — dominating category
//   TRUE:  0.22727272727272727  // ~23%
// }
```

### Other Operations

Accumulation of fuzzy sums with value normalization in the end:

```JavaScript
const values: Logic[] = [
  new Logic(0.10, 0.15, 0.20, 0.25, 0.30),
  new Logic(0.30, 0.25, 0.20, 0.15, 0.10),
  new Logic(0.20, 0.25, 0.30, 0.10, 0.15),
  new Logic(0.15, 0.20, 0.25, 0.30, 0.10)
];
const sum: Logic = new Logic();
for (let index = 0; index < values.length; index += 1) {
  sum.add(values[index]);
}
sum.asValues()
// => {
//   UNDEF: 0.75,
//   FALSE: 0.85,
//   NEVER: 0.95,
//   MAYBE: 0.8,
//   TRUE:  0.65
// }
sum.normalize().asValues()
// => {
//   UNDEF: 0.1875, // 18.75%
//   FALSE: 0.2125, // 21.25%
//   NEVER: 0.2375, // 23.75%
//   MAYBE: 0.2,    // 20.00%
//   TRUE:  0.1625  // 16.25%
// }
```

### Classical Fuzzy Logic

#### Math Background

<p align="center"><img src="/tex/9e8c19a3a784ae46cee13231f97d815b.svg?invert_in_darkmode&sanitize=true" align=middle width=87.6976551pt height=12.785402849999999pt/></p>

<p align="center"><img src="/tex/acd51acdfd66438da15a21dcec5e3318.svg?invert_in_darkmode&sanitize=true" align=middle width=119.1589377pt height=16.438356pt/></p>

<p align="center"><img src="/tex/9e0fe191cf439b1f56325ef74b4afbd7.svg?invert_in_darkmode&sanitize=true" align=middle width=114.13610339999998pt height=16.438356pt/></p>

#### Usage

```JavaScript
FUZZY_FALSE
// => 0.0
FUZZY_TRUE
// => 1.0
not(0.67)
// => 0.33
and(0.47, 0.91)
// => 0.47
or(0.75, 0.34)
// => 0.75
normalize(1.66) === FUZZY_TRUE
// => true
normalize(-28.45) === FUZZY_FALSE
// => true
normalize(0.64)
// => 0.64
```

### Optimized Imports

```JavaScript
// Discrete Common Sense Logic only
import { Categories, not, and, or, UNDEF, FALSE, NEVER, MAYBE, TRUE } from 'human-logic/dist/Category';
```

```JavaScript
// Fuzzy Common Sense Logic only
import { Logic, not, and, or, normalize } from 'human-logic/dist/Logic';
// When using class methods only
import { Logic } from 'human-logic/dist/Logic';
```

```JavaScript
// Classical Fuzzy Logic only
import { Fuzzy, not, and, or, normalize, FUZZY_TRUE, FUZZY_FALSE } from 'human-logic/dist/Fuzzy';
```
