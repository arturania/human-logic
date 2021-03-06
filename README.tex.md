# Human Logic or Common Sense

[![Build Status](https://travis-ci.org/arturania/human-logic.svg?branch=master)](https://travis-ci.org/arturania/human-logic) [![Coverage Status](https://coveralls.io/repos/github/arturania/human-logic/badge.svg?branch=master)](https://coveralls.io/github/arturania/human-logic?branch=master) ![NPM version](https://img.shields.io/npm/v/human-logic.svg) ![License](https://img.shields.io/github/license/arturania/human-logic.svg)

Human Logic (also known as “common sense”) is based on five categories:

- `true` = certainly positive
- `false` = certainly negative
- `maybe` = uncertain (could be either positive or negative)
- `never` = impossible (neither positive nor negative)
- `undefined` = totally unknown

This package provides the implementation of both Discrete Common Sense Logic and Fuzzy Common Sense Logic.

Discrete Common Sense Logic only allows `true`, `false`, `maybe`, `never` or `undefined` as a value.

In Fuzzy Common Sense Logic the value is five-dimensional unit vector. Each vector component is a fuzzy value (between 0.0 and 1.0 inclusive) of respective `true`, `false`, `maybe`, `never` or `undefined` category.

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
  Category, Categories, UNDEF, FALSE, NEVER, MAYBE, TRUE,
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
  Category, Categories, UNDEF, FALSE, NEVER, MAYBE, TRUE,
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
Category[UNDEF]
// => 'UNDEF'
Categories
// => [UNDEF, FALSE, NEVER, MAYBE, TRUE]
```

## Fuzzy Common Sense Logic

### Math Background

$$ \operatorname{!}a = \begin{cases}
\texttt{undef} = a_\texttt{undef} \\
\texttt{false} = a_\texttt{true} \\
\texttt{never} = a_\texttt{maybe} \\
\texttt{maybe} = a_\texttt{never} \\
\texttt{true} = a_\texttt{false} \\
\end{cases} $$

$$ a \operatorname{\&} b = \begin{cases}
\texttt{undef} = a_\texttt{undef} \operatorname{|} b_\texttt{undef} \\
\texttt{false} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big(a_\texttt{false} \operatorname{|} b_\texttt{false} \operatorname{|} (a_\texttt{never} \operatorname{\&} b_\texttt{maybe}) | (a_\texttt{maybe} \operatorname{\&} b_\texttt{never})\Big) \\
\texttt{never} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big((a_\texttt{never} \operatorname{\&} b_\texttt{never}) | (a_\texttt{never} \operatorname{\&} b_\texttt{true}) | (a_\texttt{true} \operatorname{\&} b_\texttt{never})\Big) \\
\texttt{maybe} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big((a_\texttt{maybe} \operatorname{\&} b_\texttt{maybe}) | (a_\texttt{maybe} \operatorname{\&} b_\texttt{true}) | (a_\texttt{true} \operatorname{\&} b_\texttt{maybe})\Big) \\
\texttt{true} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} a_\texttt{true} \operatorname{\&} b_\texttt{true} \\
\end{cases} $$

$$ a \operatorname{|} b = \begin{cases}
\texttt{undef} = a_\texttt{undef} \operatorname{|} b_\texttt{undef} \\
\texttt{false} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} a_\texttt{false} \operatorname{\&} b_\texttt{false} \\
\texttt{never} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big((a_\texttt{never} \operatorname{\&} b_\texttt{never}) | (a_\texttt{never} \operatorname{\&} b_\texttt{false}) | (a_\texttt{false} \operatorname{\&} b_\texttt{never})\Big) \\
\texttt{maybe} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big((a_\texttt{maybe} \operatorname{\&} b_\texttt{maybe}) | (a_\texttt{maybe} \operatorname{\&} b_\texttt{false}) | (a_\texttt{false} \operatorname{\&} b_\texttt{maybe})\Big) \\
\texttt{true} = \operatorname{!} a_\texttt{undef} \operatorname{\&} \operatorname{!}b_\texttt{undef} \operatorname{\&} \Big(a_\texttt{true} \operatorname{|} b_\texttt{true} \operatorname{|} (a_\texttt{never} \operatorname{\&} b_\texttt{maybe}) | (a_\texttt{maybe} \operatorname{\&} b_\texttt{never})\Big) \\
\end{cases} $$

where "$\operatorname{!}$", "$\operatorname{\&}$" and "$\operatorname{|}$" are [classical fuzzy logic](#classical-fuzzy-logic) operations.

### Initialization

```JavaScript
// new instance
const value = new Logic(0.1, 0.2, 0.3, 0.1, 0.4);
// or
const value = Logic.fromHash({
  _undef: 0.1,
  _false: 0.2,
  _never: 0.3,
  _maybe: 0.1,
  _true:  0.4 // — dominating category
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
value.asHash()
// => { _undef: 0.0, _false: 0.0, _never: 0.0, _maybe: 1.0, _true: 0.0 }
value.asValues()
// => { [UNDEF]: 0.0, [FALSE]: 0.0, [NEVER]: 0.0, [MAYBE]: 1.0, [TRUE]: 0.0 }

// Cloning
const clonedValue = value.clone();
clonedValue.asValues()
// => { [UNDEF]: 0.0, [FALSE]: 0.0, [NEVER]: 0.0, [MAYBE]: 1.0, [TRUE]: 0.0 }
clonedValue === value
// false

// Normalization
const nonNormalizedValue = Logic.fromHash({
  _undef: 2,
  _false: 3,
  _never: 4,
  _maybe: 5,
  _true:  6
});
const normalizedValue = nonNormalizedValue.normalize();
normalizedValue.asArray()
// => [0.1, 0.15, 0.2, 0.25, 0.3]
nonNormalizedValue.getNormalized(NEVER)
// => 0.2
```

### Logical NOT

```JavaScript
const value = Logic.fromHash({
  _undef: 0.10, // 10%
  _false: 0.15, // 15%
  _never: 0.20, // 20%
  _maybe: 0.25, // 25%
  _true:  0.30  // 30% — dominating category
});

// Use either class method:
value.not().asHash()
// or polymorphic function:
not(value).asHash()
// => {
//   _undef: 0.1,  // 10%
//   _false: 0.3,  // 30% — dominating category
//   _never: 0.25, // 25%
//   _maybe: 0.2,  // 20%
//   _true:  0.15  // 15%
// }
```

### Logical AND

```JavaScript
const value1 = Logic.fromHash({
  _undef: 0.15, // 15%
  _false: 0.10, // 10%
  _never: 0.25, // 25%
  _maybe: 0.30, // 30% — dominating category
  _true:  0.20  // 20%
});
const value2 = Logic.fromHash({
  _undef: 0.20, // 20%
  _false: 0.30, // 30% — dominating category
  _never: 0.10, // 10%
  _maybe: 0.15, // 15%
  _true:  0.25  // 25%
});

// class method
value1.and(value2).asHash()
// polymorphic function
and(value1, value2).asHash()
// => {
//   _undef: 0.16666666666666669, // ~17%
//   _false: 0.25,                //  25% — dominating category
//   _never: 0.20833333333333334, // ~21%
//   _maybe: 0.20833333333333334, // ~21%
//   _true:  0.16666666666666669  // ~17%
// }
```

### Logical OR

```JavaScript
// class method
value1.or(value2).asHash()
// polymorphic function
or(value1, value2).asHash()
// => {
//   _undef: 0.18181818181818182, // ~18%
//   _false: 0.09090909090909091, //  ~9%
//   _never: 0.22727272727272727, // ~23%
//   _maybe: 0.2727272727272727,  // ~27% — dominating category
//   _true:  0.22727272727272727  // ~23%
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
sum.asHash()
// => {
//   _undef: 0.75,
//   _false: 0.85,
//   _never: 0.95,
//   _maybe: 0.8,
//   _true:  0.65
// }
sum.normalize().asHash()
// => {
//   _undef: 0.1875, // 18.75%
//   _false: 0.2125, // 21.25%
//   _never: 0.2375, // 23.75%
//   _maybe: 0.2,    // 20.00%
//   _true:  0.1625  // 16.25%
// }
```

### Classical Fuzzy Logic

#### Math Background

$$ \operatorname{!}a = 1.0 - a $$

$$ a \operatorname{\&} b = \operatorname{min}(a, b) $$

$$ a \operatorname{|} b = \operatorname{max}(a, b) $$

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
import { Category, Categories, not, and, or, UNDEF, FALSE, NEVER, MAYBE, TRUE } from 'human-logic/dist/Category';
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
