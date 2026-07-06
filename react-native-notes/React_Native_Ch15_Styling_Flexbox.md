# Chapter 15 — Styling & Flexbox in React Native

> **For the teacher:** Teaching notes are marked with `📌 TEACHER NOTE`.
> Code blocks are ready to live-code. Exercises are at the end.
>
> **For the student (after class):** Read through section by section.
> Every concept has an explanation, an analogy, and working code.
> Try the exercises on your own.

---

## Table of Contents

- [How Styling Works in React Native](#styling)
- [StyleSheet.create() vs Plain Objects](#stylesheet)
- [Platform-Specific Styles](#platform)
- [Flexbox — Deep Dive](#flexbox)
  - [What is Flexbox?](#what-is-flexbox)
  - [Main Axis vs Cross Axis](#axes)
  - [flexDirection](#flexdirection)
  - [justifyContent](#justifycontent)
  - [alignItems](#alignitems)
  - [alignSelf](#alignself)
  - [flex](#flex)
  - [flexWrap](#flexwrap)
- [Building Real Layouts](#layouts)
  - [Layout 1 — Simple Three-Section Column](#layout1)
  - [Layout 2 — Stretch to Fill Width](#layout2)
  - [Layout 3 — Flexible Row](#layout3)
  - [Layout 4 — Wrapping Grid](#layout4)
  - [Layout 5 — Nested Rows and Columns](#layout5)
- [Flexbox Quick Reference](#reference)
- [Exercises](#exercises)

---

<a name="styling"></a>
## How Styling Works in React Native

In web React you write CSS — either in `.css` files, CSS Modules, or inline
styles. In React Native there are **no CSS files at all**. Styles are written
as **plain JavaScript objects** and applied via the `style` prop.

```jsx
// Web React
<div className="container">Hello</div>

// React Native
<View style={styles.container}>Hello</View>
```

You define your styles using `StyleSheet.create()`:

```jsx
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: 'white',
    alignItems: 'center',
    justifyContent: 'center',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#333',
  },
});
```

> 📌 **TEACHER NOTE:** Ask students — "What looks familiar here?" They should
> recognise the camelCase property names from inline CSS in web React.
> `background-color` becomes `backgroundColor`, `font-size` becomes `fontSize`,
> `font-weight` becomes `fontWeight`. The naming convention is identical to
> inline styles in web React. The only difference is there's no CSS file —
> it's all just JavaScript.

---

<a name="stylesheet"></a>
## StyleSheet.create() vs Plain Objects

You could write styles as a plain JS object directly on the element:

```jsx
// This works but is not recommended
<View style={{ backgroundColor: 'red', flex: 1 }}>
```

But `StyleSheet.create()` is preferred because:

- **Validation** — it catches typos and invalid property names at development time
- **Performance** — styles are processed more efficiently when defined this way
- **Organisation** — keeps styles separate from JSX, making code cleaner
- **Readability** — named styles (`styles.container`) are more descriptive
  than inline objects

Think of `StyleSheet.create()` as the React Native equivalent of a CSS class.

---

<a name="platform"></a>
## Platform-Specific Styles

iOS and Android have different status bar heights. React Native gives you
`Platform.select()` to handle differences between platforms in one place:

```jsx
import { Platform, StyleSheet, StatusBar } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
});
```

> 📌 **TEACHER NOTE:** Break down the `...` spread operator here. `Platform.select()`
> returns an object — either `{ paddingTop: 20 }` on iOS or
> `{ paddingTop: StatusBar.currentHeight }` on Android. The spread merges that
> object into the parent style object. This is just standard JavaScript spread
> syntax applied to styles.
>
> Also note: with the modern Expo `expo-status-bar` package, the status bar
> padding is often handled automatically. But knowing `Platform.select()` is
> still important for any other platform-specific differences you encounter.

---

<a name="flexbox"></a>
## Flexbox — Deep Dive

<a name="what-is-flexbox"></a>
### What is Flexbox?

Before Flexbox, laying out elements on screen was error-prone. Flexbox
(Flexible Box) is a layout model that gives a **container** control over how
its **children** are sized and positioned.

React Native uses Flexbox as its **primary and only** layout system. Unlike
the web where you have to opt in with `display: flex`, in React Native
**every `<View>` is already a flex container by default**.

```
┌──────────────────────────────────────┐
│         FLEX CONTAINER               │
│         (the parent View)            │
│                                      │
│  ┌──────────┐ ┌──────────┐ ┌──────┐ │
│  │  Child 1 │ │  Child 2 │ │  ... │ │
│  └──────────┘ └──────────┘ └──────┘ │
│                                      │
└──────────────────────────────────────┘
```

The container decides:
- Which **direction** children are arranged
- How **space** is distributed between children
- How children are **aligned**

---

<a name="axes"></a>
### The Two Axes — The Most Important Concept

Every flex container has two axes. Understanding these is the key to
understanding every other Flexbox property.

**When `flexDirection: 'column'` (default in React Native):**

```
         Main Axis (↓ top to bottom)
              │
     ┌────────▼────────┐
     │    Child 1      │──── Cross Axis (→ left to right)
     ├─────────────────┤
     │    Child 2      │
     ├─────────────────┤
     │    Child 3      │
     └─────────────────┘
```

**When `flexDirection: 'row'`:**

```
 Main Axis (→ left to right)
 ──────────────────────────────▶
 ┌─────────┐ ┌─────────┐ ┌─────────┐
 │  Child 1 │ │  Child 2 │ │  Child 3 │
 └─────────┘ └─────────┘ └─────────┘
      │
      ▼ Cross Axis (↓ top to bottom)
```

> 📌 **TEACHER NOTE:** Drill this in — it's the root of all Flexbox confusion.
> Write these two rules on the board and leave them there for the whole lesson:
>
> **Rule 1:** `justifyContent` always controls the MAIN AXIS
> **Rule 2:** `alignItems` always controls the CROSS AXIS
>
> These two rules never change regardless of `flexDirection`. Every question
> a student has about "why isn't this centering?" comes back to not knowing
> which axis they're working with.

---

<a name="flexdirection"></a>
### `flexDirection`

Controls which direction children are stacked — the main axis direction.

```jsx
flexDirection: 'column'   // default — children stack top to bottom
flexDirection: 'row'      // children sit side by side left to right
```

```
'column' (default)        'row'
┌──────────────┐          ┌────┬────┬────┐
│   Child 1    │          │ 1  │ 2  │ 3  │
├──────────────┤          └────┴────┴────┘
│   Child 2    │
├──────────────┤
│   Child 3    │
└──────────────┘
```

> 📌 **TEACHER NOTE:** Emphasise that `'column'` is the DEFAULT in React Native.
> This is different from CSS on the web where `flex-direction` defaults to `'row'`.
> This catches web developers off guard. In React Native, things stack vertically
> by default.

---

<a name="justifycontent"></a>
### `justifyContent`

Controls how children are distributed along the **main axis**.

```jsx
justifyContent: 'flex-start'    // pack at the start (default)
justifyContent: 'flex-end'      // pack at the end
justifyContent: 'center'        // pack in the centre
justifyContent: 'space-between' // even gaps between, no edge gaps
justifyContent: 'space-around'  // even gaps around each child
justifyContent: 'space-evenly'  // perfectly even gaps everywhere
```

Visual (with `flexDirection: 'column'` — main axis is vertical):

```
flex-start    center      flex-end    space-between   space-around
┌────────┐   ┌────────┐  ┌────────┐  ┌────────┐      ┌────────┐
│ [■]    │   │        │  │        │  │ [■]    │      │        │
│ [■]    │   │  [■]   │  │        │  │        │      │  [■]   │
│ [■]    │   │  [■]   │  │        │  │ [■]    │      │        │
│        │   │  [■]   │  │  [■]   │  │        │      │  [■]   │
│        │   │        │  │  [■]   │  │ [■]    │      │        │
│        │   │        │  │  [■]   │  │        │      │  [■]   │
└────────┘   └────────┘  └────────┘  └────────┘      └────────┘
```

---

<a name="alignitems"></a>
### `alignItems`

Controls how children are aligned along the **cross axis**.

```jsx
alignItems: 'stretch'     // default — children stretch to fill cross axis
alignItems: 'flex-start'  // children align to start of cross axis
alignItems: 'flex-end'    // children align to end of cross axis
alignItems: 'center'      // children centre on cross axis
```

Visual (with `flexDirection: 'column'` — cross axis is horizontal):

```
stretch              center             flex-start         flex-end
┌────────────┐       ┌────────────┐     ┌────────────┐     ┌────────────┐
│████████████│       │   [■■■]    │     │[■■■]       │     │       [■■■]│
│████████████│       │   [■■■]    │     │[■■■]       │     │       [■■■]│
│████████████│       │   [■■■]    │     │[■■■]       │     │       [■■■]│
└────────────┘       └────────────┘     └────────────┘     └────────────┘
(fills width)        (centred)          (left-aligned)      (right-aligned)
```

---

<a name="alignself"></a>
### `alignSelf`

`alignItems` is set on the **parent** and affects all children.
`alignSelf` is set on an **individual child** to override the parent's
`alignItems` for just that one child.

```jsx
// Parent centres all children
<View style={{ alignItems: 'center' }}>

  <View style={{ width: 100, height: 50, backgroundColor: 'blue' }} />
  {/* ↑ centred by parent */}

  <View style={{
    height: 50,
    backgroundColor: 'red',
    alignSelf: 'stretch'  // ← overrides parent, stretches to full width
  }} />

</View>
```

```
Result:
┌──────────────────────────┐
│       [blue box]         │  ← centred (from parent alignItems)
├──────────────────────────┤
│██████████████████████████│  ← stretched (alignSelf overrides)
└──────────────────────────┘
```

---

<a name="flex"></a>
### `flex`

Controls how much **space** a child takes up relative to its siblings.
Think of it as giving each child a share of the available space.

```jsx
// One child with flex: 1 takes ALL available space
<View style={{ flex: 1 }}>

// Two children sharing space proportionally
<View style={{ flex: 1 }}>  {/* gets 1/3 of the space */}
<View style={{ flex: 2 }}>  {/* gets 2/3 of the space */}
```

Visual:

```
flex: 1 on container — fills the entire screen height:
┌──────────────────────────┐
│                          │
│   (fills whole screen)   │
│                          │
└──────────────────────────┘

Two children — flex: 1 and flex: 2:
┌──────────────────────────┐
│  Child A  (flex: 1) ─ 1/3│
├──────────────────────────┤
│                          │
│  Child B  (flex: 2) ─ 2/3│
│                          │
└──────────────────────────┘
```

> 📌 **TEACHER NOTE:** Always give the root container `flex: 1`. Without it,
> the View has no height and nothing renders correctly. This is the single most
> common mistake beginners make. When a student says "my screen is blank" or
> "my layout isn't showing" — check for `flex: 1` on the container first.

---

<a name="flexwrap"></a>
### `flexWrap`

By default, children that don't fit in one row/column overflow or get clipped.
`flexWrap: 'wrap'` makes them flow to the next line automatically.

```jsx
flexWrap: 'nowrap'  // default — all children forced into one line
flexWrap: 'wrap'    // children wrap to the next row/column
```

Visual with `flexDirection: 'row'` and `flexWrap: 'wrap'`:

```
Without wrap:                 With wrap:
┌─────────────────────┐       ┌─────────────────────┐
│ [1][2][3][4][5]►    │       │  [1]   [2]   [3]    │
│  cut off →          │       │  [4]   [5]   [6]    │
└─────────────────────┘       │  [7]   [8]          │
                              └─────────────────────┘
```

Useful for grid layouts where you have a dynamic number of items.

---

<a name="layouts"></a>
## Building Real Layouts

Now let's put everything together with practical examples that go from
simple to complex.

---

<a name="layout1"></a>
### Layout 1 — Simple Three-Section Column

Three sections stacked vertically, equally spaced.

```jsx
import { View, Text, StyleSheet, Platform, StatusBar } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <View style={styles.box}>
        <Text style={styles.boxText}>#1</Text>
      </View>
      <View style={styles.box}>
        <Text style={styles.boxText}>#2</Text>
      </View>
      <View style={styles.box}>
        <Text style={styles.boxText}>#3</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    alignItems: 'center',
    justifyContent: 'space-around',
    backgroundColor: 'ghostwhite',
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
  box: {
    width: 300,
    height: 100,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'lightgray',
    borderWidth: 1,
    borderStyle: 'dashed',
    borderColor: 'darkslategray',
  },
  boxText: {
    color: 'darkslategray',
    fontWeight: 'bold',
    fontSize: 18,
  },
});
```

Result:

```
┌──────────────────────────┐
│                          │
│   ┌──────────────────┐   │
│   │        #1        │   │
│   └──────────────────┘   │
│                          │
│   ┌──────────────────┐   │
│   │        #2        │   │
│   └──────────────────┘   │
│                          │
│   ┌──────────────────┐   │
│   │        #3        │   │
│   └──────────────────┘   │
│                          │
└──────────────────────────┘
```

> 📌 **TEACHER NOTE:** Walk through each style property and ask students to
> predict the outcome before showing it. `justifyContent: 'space-around'` —
> "what do you think this does to the three boxes?" Let them guess, then show.
> Active prediction makes the lesson stick better than passive observation.

---

<a name="layout2"></a>
### Layout 2 — Boxes That Stretch Full Width

**The problem with Layout 1:** The boxes have a fixed `width: 300`. When you
rotate the phone to landscape mode, the boxes don't adapt — you get wasted space
on the sides.

**The fix:** Remove the fixed width and use `alignSelf: 'stretch'` so the
boxes automatically fill whatever width is available.

Change only the `box` style:

```jsx
box: {
  height: 100,
  // width: 300   ← REMOVED
  justifyContent: 'center',
  alignSelf: 'stretch',    // ← fills available width automatically
  alignItems: 'center',
  backgroundColor: 'lightgray',
  borderWidth: 1,
  borderStyle: 'dashed',
  borderColor: 'darkslategray',
},
```

```
Portrait:                     Landscape:
┌──────────────────────────┐  ┌──────────────────────────────────────┐
│ ┌──────────────────────┐ │  │ ┌──────────────────────────────────┐ │
│ │          #1          │ │  │ │               #1                 │ │
│ └──────────────────────┘ │  │ └──────────────────────────────────┘ │
│ ┌──────────────────────┐ │  │ ┌──────────────────────────────────┐ │
│ │          #2          │ │  │ │               #2                 │ │
│ └──────────────────────┘ │  │ └──────────────────────────────────┘ │
└──────────────────────────┘  └──────────────────────────────────────┘
```

> 📌 **TEACHER NOTE:** Rotate the phone or emulator live and show the
> difference between Layout 1 (fixed width, gaps on sides) and Layout 2
> (stretches to fill). Students immediately see why `alignSelf: 'stretch'`
> is more useful than a fixed width for responsive layouts.

---

<a name="layout3"></a>
### Layout 3 — Flexible Row Layout

Two sections side by side, each stretching full height.

Change `flexDirection` to `'row'` and use `alignSelf: 'stretch'` on the boxes:

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',          // ← children now go left to right
    backgroundColor: 'ghostwhite',
    alignItems: 'center',
    justifyContent: 'space-around',
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
  box: {
    width: 100,
    justifyContent: 'center',
    alignSelf: 'stretch',          // ← now stretches TOP TO BOTTOM
    alignItems: 'center',
    backgroundColor: 'lightgray',
    borderWidth: 1,
    borderStyle: 'dashed',
    borderColor: 'darkslategray',
  },
  boxText: {
    color: 'darkslategray',
    fontWeight: 'bold',
  },
});
```

Result:

```
┌──────────────────────────┐
│  ┌─────────┐ ┌─────────┐ │
│  │         │ │         │ │
│  │         │ │         │ │
│  │   #1    │ │   #2    │ │
│  │         │ │         │ │
│  │         │ │         │ │
│  └─────────┘ └─────────┘ │
└──────────────────────────┘
```

> 📌 **TEACHER NOTE:** Ask students: "Earlier, `alignSelf: 'stretch'` made
> boxes wider. Now it makes them taller. Why?"
>
> Because `flexDirection` is `'row'`, the main axis is now horizontal and the
> cross axis is now vertical. `alignSelf: 'stretch'` always stretches in the
> cross axis direction. So it stretches vertically — top to bottom.
>
> This is the moment students either get Flexbox or don't. Pause here and let
> the question settle before moving on.

---

<a name="layout4"></a>
### Layout 4 — Wrapping Grid

A grid of equal-sized items that automatically wrap to the next row.
The number of items doesn't matter — the layout handles it.

```jsx
import { View, StyleSheet, StatusBar, Platform } from 'react-native';
import Box from './Box'; // reusable box component

const boxes = new Array(10).fill(null).map((_, i) => i + 1);

export default function App() {
  return (
    <View style={styles.container}>
      {boxes.map(i => (
        <Box key={i}>#{i}</Box>
      ))}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',    // children go left to right
    flexWrap: 'wrap',        // wrap to next row when edge is reached
    backgroundColor: 'ghostwhite',
    alignItems: 'center',
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
  // Box styles (if not using a separate component)
  box: {
    height: 100,
    width: 100,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'lightgray',
    borderWidth: 1,
    borderStyle: 'dashed',
    borderColor: 'darkslategray',
    margin: 10,
  },
});
```

Result:

```
Portrait:                    Landscape:
┌────────────────────┐       ┌──────────────────────────────┐
│ [1]  [2]  [3]      │       │ [1]  [2]  [3]  [4]  [5]     │
│ [4]  [5]  [6]      │       │ [6]  [7]  [8]  [9]  [10]    │
│ [7]  [8]  [9]      │       └──────────────────────────────┘
│ [10]               │
└────────────────────┘
```

Rotating the phone automatically reflows the grid. No media queries.
No extra code. Just Flexbox doing its job.

---

<a name="layout5"></a>
### Layout 5 — Nested Rows and Columns

For sophisticated layouts, you nest rows inside columns or columns inside rows.
The key is to create small reusable layout components.

**`Row.js` — a view that lays children horizontally:**

```jsx
import { View, StyleSheet } from 'react-native';

export default function Row({ children }) {
  return <View style={styles.row}>{children}</View>;
}

const styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    flex: 1,
  },
});
```

**`Column.js` — a view that lays children vertically:**

```jsx
import { View, StyleSheet } from 'react-native';

export default function Column({ children }) {
  return <View style={styles.column}>{children}</View>;
}

const styles = StyleSheet.create({
  column: {
    flexDirection: 'column',
    flex: 1,
  },
});
```

**`App.js` — composing them together:**

```jsx
import { View, StyleSheet, Platform, StatusBar } from 'react-native';
import Row from './Row';
import Column from './Column';
import Box from './Box';

export default function App() {
  return (
    <View style={styles.container}>

      <Row>
        <Column>
          <Box>#1</Box>
          <Box>#2</Box>
        </Column>
        <Column>
          <Box>#3</Box>
          <Box>#4</Box>
        </Column>
      </Row>

      <Row>
        <Column>
          <Box>#5</Box>
          <Box>#6</Box>
        </Column>
        <Column>
          <Box>#7</Box>
          <Box>#8</Box>
        </Column>
      </Row>

    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
});
```

Result:

```
┌──────────────────────────┐
│  ┌──────────┬──────────┐ │
│  │    #1    │    #3    │ │
│  ├──────────┼──────────┤ │
│  │    #2    │    #4    │ │
│  └──────────┴──────────┘ │
│  ┌──────────┬──────────┐ │
│  │    #5    │    #7    │ │
│  ├──────────┼──────────┤ │
│  │    #6    │    #8    │ │
│  └──────────┴──────────┘ │
└──────────────────────────┘
```

> 📌 **TEACHER NOTE:** Ask: "Why is #2 below #1 and not beside it?"
> Because #1 and #2 are both inside a `<Column>`, which stacks vertically.
> Then ask: "Why are #1/#2 and #3/#4 side by side?"
> Because the two Columns are inside a `<Row>`, which stacks horizontally.
>
> This is the "aha" moment for nested layouts. Nesting is just Flexbox
> containers inside other Flexbox containers.

---

<a name="reference"></a>
## Flexbox Quick Reference

### Properties Summary

| Property | Applied On | What It Controls |
|---|---|---|
| `flexDirection` | Parent | Main axis: `'row'` or `'column'` |
| `justifyContent` | Parent | Distribution along the main axis |
| `alignItems` | Parent | Alignment along the cross axis (all children) |
| `alignSelf` | Child | Overrides parent's `alignItems` for this child only |
| `flex` | Child | How much space this child takes relative to siblings |
| `flexWrap` | Parent | Whether children wrap to next line |

### The Two Rules to Always Remember

```
Rule 1: justifyContent → MAIN AXIS
Rule 2: alignItems     → CROSS AXIS

When flexDirection is 'column':
  Main axis = vertical   (top → bottom)
  Cross axis = horizontal (left → right)

When flexDirection is 'row':
  Main axis = horizontal (left → right)
  Cross axis = vertical   (top → bottom)
```

### `justifyContent` Values

| Value | What It Does |
|---|---|
| `'flex-start'` | Pack children at the start (default) |
| `'flex-end'` | Pack children at the end |
| `'center'` | Pack children in the centre |
| `'space-between'` | Equal gaps between children, no edge gaps |
| `'space-around'` | Equal gaps around each child |
| `'space-evenly'` | Perfectly equal gaps everywhere |

### `alignItems` Values

| Value | What It Does |
|---|---|
| `'stretch'` | Stretch children to fill cross axis (default) |
| `'flex-start'` | Align to start of cross axis |
| `'flex-end'` | Align to end of cross axis |
| `'center'` | Centre children on cross axis |

---

<a name="exercises"></a>
## Chapter 15 Exercises

### Exercise 1 — Centre Everything
Create a screen with a single coloured box centred both horizontally and
vertically on the screen. Use `justifyContent` and `alignItems` on the
container.

### Exercise 2 — Proportional Split
Create a screen with three sections stacked vertically where:
- Section 1 takes 25% of the screen (`flex: 1`)
- Section 2 takes 50% of the screen (`flex: 2`)
- Section 3 takes 25% of the screen (`flex: 1`)

Give each section a different background colour and a centred label.

### Exercise 3 — Classic App Layout
Recreate this common mobile app layout using only Flexbox:

```
┌──────────────────────────┐
│         HEADER           │  ← fixed height 60, blue background
├──────────────────────────┤
│                          │
│         CONTENT          │  ← fills remaining space (flex: 1)
│                          │
├──────────────────────────┤
│         FOOTER           │  ← fixed height 60, grey background
└──────────────────────────┘
```

### Exercise 4 — The Grid
Render 12 boxes in a wrapping grid layout using `flexDirection: 'row'` and
`flexWrap: 'wrap'`. Each box should be 80x80 with a number inside.
Verify it reflows correctly when you rotate to landscape.

### Exercise 5 — Nested Layout Challenge
Build a layout with:
- Two rows
- Each row has three equal columns
- Each column has a box with a number inside
- Total: 6 numbered boxes arranged in a 2-row × 3-column grid

Use separate `Row` and `Column` components like in Layout 5.

---

*Chapter 15 — Styling and Flexbox in React Native.*
*StyleSheet API is current and unchanged.*
*`create-react-native-app` reference in original slides is deprecated — ignored here.*
