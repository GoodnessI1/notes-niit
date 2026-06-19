# React Native: From Setup to Navigation
### Chapters 14, 15 & 16 — Comprehensive Teaching Guide

> **For the teacher:** Teaching notes are marked with `📌 TEACHER NOTE`. Code blocks are
> ready to live-code. Exercises are at the end of each chapter.
>
> **For the student (after class):** Read through section by section. Every concept has
> an explanation, an analogy, and working code. Try the exercises on your own.

---

## Table of Contents

- [Chapter 14 — Getting Started with React Native & Expo](#chapter-14)
- [Chapter 15 — Styling & Flexbox in React Native](#chapter-15)
- [Chapter 16 — Navigation with React Navigation](#chapter-16)

---

<a name="chapter-14"></a>
# Chapter 14 — Getting Started with React Native & Expo

## What is React Native?

You already know React. You know components, JSX, props, state, and hooks. React Native
is React — but instead of rendering to the browser DOM, it renders to **native mobile UI
components** on iOS and Android.

Think of it this way:

```
React (Web)             React Native (Mobile)
─────────────────       ─────────────────────
<div>          →        <View>
<p>            →        <Text>
<img>          →        <Image>
<button>       →        <TouchableOpacity> or <Pressable>
CSS files      →        StyleSheet (JavaScript objects)
React DOM      →        React Native runtime
```

The mental model is the same. Components, props, state, hooks — all identical.
What changes is the **primitives** (the building block tags) and the **styling system**.

---

## What is Expo?

Setting up a raw React Native project requires installing Xcode (for iOS), Android
Studio, Java, SDKs, emulators, and configuring environment variables. That's a lot
before you even write one line of code.

**Expo** solves this problem. It is a toolchain and platform built on top of React Native
that removes all that setup friction. With Expo you can:

- Scaffold a new project in one command
- Run your app on a real phone without building anything
- Use a browser-based playground (Expo Snack)
- Access device features (camera, location, notifications) without native code

> 📌 **TEACHER NOTE:** A good analogy here — Expo is to React Native what
> Create React App (or Vite) is to React web. It is the recommended starting point.

---

## A Brief History of Expo's Setup Commands

> 📌 **TEACHER NOTE:** Mention this so students aren't confused when they find
> older tutorials online.

The setup commands have changed over time:

```
2017 → create-react-native-app     (deprecated — was a thin wrapper around Expo)
         ↓
2018 → expo-cli / expo init        (deprecated — the global CLI was retired)
         ↓
2022 → npx create-expo-app         ✅ CURRENT — this is what we use today
```

The **concept** has always been the same — generate a boilerplate project with all
the scaffolding in place. Only the command changed.

---

## Installing and Creating a Project

### Step 1 — Install Node.js

Expo requires Node.js. If you don't have it:

```bash
# Check if you have it
node -v

# Should return something like v18.x.x or higher
```

Download from https://nodejs.org if needed.

---

### Step 2 — Create a New Project

No global installation needed. Just run:

```bash
npx create-expo-app my-project
```

You will be prompted to choose a template:

```
? Choose a template:
  Blank
  Blank (TypeScript)
  Navigation (TypeScript)
  ─────────────────────
  ...
```

Choose **Blank** for now. This gives you a minimal app — a clean slate.

What gets created:

```
my-project/
├── App.js              ← Your root component (this is where you start)
├── app.json            ← Expo configuration (app name, icon, splash screen)
├── package.json        ← Dependencies
├── node_modules/       ← Installed packages
├── assets/             ← Images, fonts, etc.
└── .gitignore
```

---

### Step 3 — Navigate into the Project and Start

```bash
cd my-project
npm start
```

This launches the **Metro Bundler** — Metro is the JavaScript bundler React Native uses
(think of it like Vite/Webpack but for mobile).

You will see output like:

```
Starting Metro Bundler
Metro waiting on exp://192.168.x.x:8081

› Press a │ open Android
› Press i │ open iOS simulator
› Press w │ open web

› Press r │ reload app
› Press m │ toggle menu
› Press ? │ show all commands
```

---

### Step 4 — View on Your Phone

1. Download the **Expo Go** app on your phone
   - Android: Google Play Store
   - iOS: Apple App Store

2. Open Expo Go and tap **Scan QR Code**

3. Point your camera at the QR code shown in the terminal

4. Your app loads on your phone — live!

> 📌 **TEACHER NOTE:** Make sure your phone and laptop are on the same Wi-Fi
> network or it won't connect. If it doesn't work, press `s` in the terminal to
> switch to Expo Go tunnel mode.

---

### Step 5 — Look at the Boilerplate App.js

Open `App.js` in your editor:

```jsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Open up App.js to start working on your app!</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

Notice:
- `View` instead of `div`
- `Text` instead of `p` (all text **must** be inside a `<Text>` component)
- `StyleSheet.create()` instead of CSS files
- No `className` — styles are applied via the `style` prop

---

## Expo Snack — Browser-Based Playground

Expo Snack is an online IDE for React Native. No installation, no phone needed.
Great for quick experiments and sharing code snippets.

Visit: **https://snack.expo.dev**

You can:
- Write and run React Native code directly in the browser
- Switch between Android, iOS, and Web previews
- Import a GitHub repository
- Share your Snack with a link

> 📌 **TEACHER NOTE:** Expo Snack is great for classroom demos. If a student's
> setup isn't working, they can still follow along at snack.expo.dev.

---

## Chapter 14 Exercises

### Exercise 1 — Your First Screen
Modify `App.js` to display your name and a short bio. Use at least two `<Text>` components
and give each one a different style (different font size, color, etc.).

### Exercise 2 — Explore the Structure
Open `app.json` and change the app `name` field. Reload the app and observe what changes.

### Exercise 3 — Snack Challenge
Go to https://snack.expo.dev and create a component that displays:
- A heading: "My First React Native App"
- A paragraph of any text below it
- Run it on the iOS and Android virtual devices

---

<a name="chapter-15"></a>
# Chapter 15 — Styling & Flexbox in React Native

## How Styling Works in React Native

In web React you write CSS — either in `.css` files, CSS modules, or inline styles.
In React Native there are **no CSS files**. Styles are written as **plain JavaScript
objects** and applied via the `style` prop.

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
});
```

> 📌 **TEACHER NOTE:** Ask students — "What looks familiar here?" They should
> recognize the camelCase property names from inline CSS in web React.
> `background-color` becomes `backgroundColor`, `font-size` becomes `fontSize` etc.

---

## StyleSheet.create() vs Plain Objects

You could just write styles as a plain JS object:

```jsx
<View style={{ backgroundColor: 'red', flex: 1 }}>
```

But `StyleSheet.create()` is preferred because:

- It validates your style properties at development time (typos get caught early)
- It sends styles to the native layer more efficiently
- It keeps your styles organized and separated from your JSX

---

## Platform-Specific Styles

Different devices have different status bar heights. React Native gives you
`Platform.select()` to handle this:

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

> 📌 **TEACHER NOTE:** The `...` spread operator merges the platform-specific
> object into the style. On iOS it adds `paddingTop: 20`, on Android it reads
> the actual status bar height dynamically.

---

## Flexbox in React Native — Deep Dive

### What is Flexbox?

Before Flexbox, laying out elements on screen was error-prone and hacky. Flexbox
(Flexible Box) is a layout model that gives a **container** control over how its
**children** are sized and positioned.

React Native uses Flexbox as its **primary and default** layout system. Unlike the
web where you have to opt in with `display: flex`, in React Native **every `<View>`
is already a flex container**.

The mental model:

```
┌──────────────────────────────────────┐
│           FLEX CONTAINER             │
│   (the parent View)                  │
│                                      │
│  ┌────────┐  ┌────────┐  ┌────────┐ │
│  │ Child  │  │ Child  │  │ Child  │ │
│  └────────┘  └────────┘  └────────┘ │
│                                      │
└──────────────────────────────────────┘
```

The container decides:
- Which **direction** children are arranged (row or column)
- How **space** is distributed between children
- How children are **aligned** on the cross axis

---

### Key Concept: Main Axis vs Cross Axis

This is the most important concept in Flexbox. Every flex container has two axes:

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
     ──────────────────────────▶
     ┌─────┐ ┌─────┐ ┌─────┐
     │  1  │ │  2  │ │  3  │
     └─────┘ └─────┘ └─────┘
        │
        ▼ Cross Axis (↓ top to bottom)
```

> 📌 **TEACHER NOTE:** This is where most students get confused. Emphasize:
> `justifyContent` always controls the **main axis**.
> `alignItems` always controls the **cross axis**.
> Those two rules never change regardless of `flexDirection`.

---

### `flexDirection`

Controls which direction children are stacked.

```jsx
// Column (default) — children stack top to bottom
<View style={{ flexDirection: 'column' }}>

// Row — children sit side by side left to right
<View style={{ flexDirection: 'row' }}>
```

```
flexDirection: 'column'     flexDirection: 'row'
┌──────────────┐            ┌────┬────┬────┐
│   Child 1    │            │ 1  │ 2  │ 3  │
├──────────────┤            └────┴────┴────┘
│   Child 2    │
├──────────────┤
│   Child 3    │
└──────────────┘
```

---

### `justifyContent`

Controls how children are distributed along the **main axis**.

```jsx
justifyContent: 'flex-start'    // Pack at the start (default)
justifyContent: 'flex-end'      // Pack at the end
justifyContent: 'center'        // Pack in the middle
justifyContent: 'space-between' // Even gaps between children, no edge gaps
justifyContent: 'space-around'  // Even gaps around each child
justifyContent: 'space-evenly'  // Perfectly even gaps everywhere
```

Visual (with `flexDirection: 'column'`):

```
flex-start    center      flex-end    space-between  space-around
┌────────┐   ┌────────┐  ┌────────┐  ┌────────┐     ┌────────┐
│ [A]    │   │        │  │        │  │ [A]    │     │        │
│ [B]    │   │  [A]   │  │        │  │        │     │  [A]   │
│ [C]    │   │  [B]   │  │        │  │ [B]    │     │        │
│        │   │  [C]   │  │  [A]   │  │        │     │  [B]   │
│        │   │        │  │  [B]   │  │ [C]    │     │        │
│        │   │        │  │  [C]   │  │        │     │  [C]   │
└────────┘   └────────┘  └────────┘  └────────┘     └────────┘
```

---

### `alignItems`

Controls how children are aligned along the **cross axis**.

```jsx
alignItems: 'stretch'    // Children stretch to fill (default)
alignItems: 'flex-start' // Children align to start of cross axis
alignItems: 'flex-end'   // Children align to end of cross axis
alignItems: 'center'     // Children center on cross axis
```

Visual (with `flexDirection: 'column'`, so cross axis is horizontal):

```
stretch          center          flex-start      flex-end
┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐
│████████████│  │   [■■■]    │  │[■■■]       │  │       [■■■]│
│████████████│  │   [■■■]    │  │[■■■]       │  │       [■■■]│
│████████████│  │   [■■■]    │  │[■■■]       │  │       [■■■]│
└────────────┘  └────────────┘  └────────────┘  └────────────┘
```

---

### `alignSelf`

While `alignItems` is set on the **parent** and applies to all children,
`alignSelf` is set on an **individual child** to override the parent's `alignItems`
for that specific child.

```jsx
// Parent says center everything
<View style={{ alignItems: 'center' }}>

  {/* This child overrides — it stretches instead */}
  <View style={{ alignSelf: 'stretch' }}>
    ...
  </View>

</View>
```

---

### `flex`

The `flex` property controls how much **space** a child takes up relative to its siblings.

```jsx
<View style={{ flex: 1 }}>   {/* Takes up ALL remaining space */}

// When siblings have different flex values:
<View style={{ flex: 1 }}>  {/* Gets 1/3 of the space */}
<View style={{ flex: 2 }}>  {/* Gets 2/3 of the space */}
```

Visual:

```
flex: 1 on parent fills the entire screen:
┌──────────────────────────┐
│                          │
│   (fills whole screen)   │
│                          │
└──────────────────────────┘

Two children with flex: 1 and flex: 2:
┌──────────────────────────┐
│  Child A (flex: 1)  ─ 1/3│
├──────────────────────────┤
│                          │
│  Child B (flex: 2)  ─ 2/3│
│                          │
└──────────────────────────┘
```

> 📌 **TEACHER NOTE:** Always give the root container `flex: 1`. Without it, the
> View has no height and nothing renders. This is the number one gotcha for beginners.

---

### `flexWrap`

By default, children that don't fit in one row/column get clipped. `flexWrap: 'wrap'`
makes them flow to the next line — perfect for grids.

```jsx
flexWrap: 'nowrap'  // Default — all children forced into one line
flexWrap: 'wrap'    // Children wrap to the next row/column
```

Visual with `flexDirection: 'row'` and `flexWrap: 'wrap'`:

```
Without wrap:                With wrap:
┌───────────────────┐        ┌───────────────────┐
│ [1][2][3][4][5]►  │        │  [1]  [2]  [3]    │
│  (cut off)        │        │  [4]  [5]  [6]    │
└───────────────────┘        │  [7]  [8]         │
                             └───────────────────┘
```

---

## Building Real Layouts

Now let's put it all together with practical layout examples.

---

### Layout 1 — Simple Three-Column (Vertical) Layout

Three sections stacked vertically, equally spaced.

```jsx
import React from 'react';
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

---

### Layout 2 — Improved: Boxes That Stretch Full Width

The problem with Layout 1: when you rotate the phone to landscape, the fixed `width: 300`
wastes space. Fix it by removing the `width` and using `alignSelf: 'stretch'`.

Change the `box` style:

```jsx
box: {
  height: 100,
  // width: 300  ← REMOVED
  justifyContent: 'center',
  alignSelf: 'stretch',   // ← KEY CHANGE: fills available width automatically
  alignItems: 'center',
  backgroundColor: 'lightgray',
  borderWidth: 1,
  borderStyle: 'dashed',
  borderColor: 'darkslategray',
},
```

Now the boxes stretch to fill the full screen width in both portrait AND landscape.

> 📌 **TEACHER NOTE:** This is a great moment to rotate the emulator/phone and
> show the difference live. Students see immediately why `alignSelf: 'stretch'`
> is more useful than a fixed width.

---

### Layout 3 — Flexible Row Layout (Side-by-Side Columns)

Two sections side by side, each stretching full height.

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',         // ← Row now, children go left to right
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
    alignSelf: 'stretch',         // ← Stretches top to bottom (cross axis is vertical now)
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

> 📌 **TEACHER NOTE:** Ask students to explain why `alignSelf: 'stretch'` now
> makes the boxes go TOP TO BOTTOM instead of left to right. The answer: because
> `flexDirection` is `'row'`, the cross axis is now vertical.

---

### Layout 4 — Flexible Grid with `flexWrap`

A grid of items that automatically wrap. You don't need to know how many items there are.

```jsx
import React from 'react';
import { View, StyleSheet, StatusBar, Platform } from 'react-native';
import Box from './Box'; // A reusable Box component

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
    flexDirection: 'row',   // Children go left to right
    flexWrap: 'wrap',       // When they reach the edge, wrap to the next row
    backgroundColor: 'ghostwhite',
    alignItems: 'center',
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
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

Rotating the phone automatically reflows the grid. No extra code needed.

---

### Layout 5 — Nesting Rows and Columns

For sophisticated layouts, you nest rows inside columns or columns inside rows.
The key is to create reusable layout components.

**The `Row` component:**

```jsx
// Row.js
import React from 'react';
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

**The `Column` component:**

```jsx
// Column.js
import React from 'react';
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

**Using them together in `App.js`:**

```jsx
import React from 'react';
import { View, StatusBar, StyleSheet, Platform } from 'react-native';
import Row from './Row';
import Column from './Column';
import Box from './Box';

export default function App() {
  return (
    <View style={styles.container}>
      <StatusBar hidden={false} />

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

> 📌 **TEACHER NOTE:** This is where it clicks for students. Show them that #2
> is below #1, not beside it — because they're both in a Column. Then show how
> the two columns sit side by side because they're in a Row. Nesting is the key.

---

## Flexbox Quick Reference

| Property | Applied On | What It Does |
|---|---|---|
| `flexDirection` | Parent | Main axis direction: `'row'` or `'column'` |
| `justifyContent` | Parent | Distribution along main axis |
| `alignItems` | Parent | Alignment along cross axis (all children) |
| `alignSelf` | Child | Overrides parent's `alignItems` for this one child |
| `flex` | Child | How much space this child takes relative to siblings |
| `flexWrap` | Parent | Whether to wrap children to next line |

---

## Chapter 15 Exercises

### Exercise 1 — Center Everything
Create a screen with a single box centered both horizontally and vertically.
Use `justifyContent` and `alignItems` on the container.

### Exercise 2 — The Proportional Split
Create a screen with three sections stacked vertically where:
- Section 1 takes up 25% of the screen (flex: 1)
- Section 2 takes up 50% of the screen (flex: 2)
- Section 3 takes up 25% of the screen (flex: 1)

Give each a different background color.

### Exercise 3 — The App Layout
Recreate this common mobile app layout using only Flexbox:

```
┌──────────────────────────┐
│         HEADER           │  ← fixed height, e.g. 60
├──────────────────────────┤
│                          │
│         CONTENT          │  ← fills remaining space (flex: 1)
│                          │
├──────────────────────────┤
│         FOOTER           │  ← fixed height, e.g. 60
└──────────────────────────┘
```

### Exercise 4 — Wrap Grid Challenge
Render 15 boxes in a wrapping grid layout. Each box should be 80x80.
They should flow naturally across the screen in rows.

---

<a name="chapter-16"></a>
# Chapter 16 — Navigation with React Navigation

## Why Navigation?

A mobile app almost always has more than one screen. You need a way to:
- Move from one screen to another
- Pass data between screens
- Go back
- Show tabs or a drawer menu

React Native doesn't have a built-in navigation solution that everyone uses.
The community settled on **React Navigation** as the standard library.

---

## Installing React Navigation

```bash
npm install @react-navigation/native

# Install peer dependencies via Expo
npx expo install react-native-screens react-native-safe-area-context
```

For **Stack Navigation** (screen-to-screen):

```bash
npm install @react-navigation/native-stack
```

For **Tab Navigation**:

```bash
npm install @react-navigation/bottom-tabs
```

For **Drawer Navigation**:

```bash
npm install @react-navigation/drawer
npx expo install react-native-gesture-handler react-native-reanimated
```

---

## ⚠️ Old API vs New API — Important Context

> 📌 **TEACHER NOTE:** Explain this section before showing any code. Students
> WILL encounter old tutorials. This table helps them recognise what's outdated.

The slides and many online tutorials show **React Navigation v4** (2019–2020).
The current version is **v6/v7** and the API changed significantly.

| Old API (v4) — DEPRECATED | New API (v6+) — USE THIS |
|---|---|
| `import { createAppContainer } from 'react-navigation'` | **Removed** |
| `import { createStackNavigator } from 'react-navigation-stack'` | `import { createNativeStackNavigator } from '@react-navigation/native-stack'` |
| `createAppContainer(createStackNavigator({...}))` | `<NavigationContainer><Stack.Navigator>` |
| `navigation.getParam('title')` | `route.params.title` |
| `Component.navigationOptions = { title: 'Home' }` | `options` prop on `<Stack.Screen>` |
| `screenProps` for passing state down | React Context |
| `createBottomTabNavigator` from `react-navigation-tabs` | from `@react-navigation/bottom-tabs` |
| `createDrawerNavigator` from `react-navigation-drawer` | from `@react-navigation/drawer` |

The core **concept** is the same — the syntax is what changed.

---

## Part 1 — Navigation Basics

### How It Works (Concept)

React Navigation works like a **stack of cards**:

```
                    ┌──────────────────┐
                    │   Details Screen  │  ← current (on top)
                 ┌──┴──────────────────┤
                 │    Home Screen       │  ← previous (below)
              ───┴──────────────────────┘

navigation.navigate('Details') → pushes Details on top of the stack
navigation.goBack()            → pops Details, returns to Home
```

---

### Old API (v4) — For Reference

> 📌 **TEACHER NOTE:** Show this briefly so students recognize it in old tutorials.
> Don't spend too much time here. Move to the new API quickly.

```jsx
// ❌ OLD - App.js (v4)
import { createAppContainer } from 'react-navigation';
import { createStackNavigator } from 'react-navigation-stack';
import Home from './Home';
import Settings from './Settings';

export default createAppContainer(
  createStackNavigator(
    { Home, Settings },
    { initialRouteName: 'Home' }
  )
);

// ❌ OLD - Home.js (v4)
export default function Home({ navigation }) {
  return (
    <Button
      title="Go to Settings"
      onPress={() => navigation.navigate('Settings')}
    />
  );
}
```

---

### New API (v6) — Current Standard ✅

```jsx
// ✅ NEW - App.js (v6)
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from './screens/HomeScreen';
import SettingsScreen from './screens/SettingsScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Settings" component={SettingsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

```jsx
// ✅ NEW - HomeScreen.js (v6)
import { View, Text, Button } from 'react-native';

export default function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Home Screen</Text>
      <Button
        title="Go to Settings"
        onPress={() => navigation.navigate('Settings')}
      />
    </View>
  );
}
```

```jsx
// ✅ NEW - SettingsScreen.js (v6)
import { View, Text, Button } from 'react-native';

export default function SettingsScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Settings Screen</Text>
      <Button
        title="Go Back"
        onPress={() => navigation.goBack()}
      />
    </View>
  );
}
```

> 📌 **TEACHER NOTE:** Walk through the structure:
> - `NavigationContainer` wraps everything — it is the navigation root
> - `Stack.Navigator` defines the navigation pattern
> - `Stack.Screen` registers each screen and maps a name to a component
> - The `navigation` prop is automatically passed to every registered screen

---

## Part 2 — Route Parameters

You often need to pass data to the next screen. For example, tapping a product
should open its details page with that product's info.

### Passing Parameters

```jsx
// From the Home screen — pass data when navigating
navigation.navigate('Details', {
  title: 'First Item',
  price: 2500,
});
```

### Old API (v4) — Receiving Parameters

```jsx
// ❌ OLD
export default function DetailsScreen({ navigation }) {
  const title = navigation.getParam('title');     // ← deprecated, removed in v5+
  const price = navigation.getParam('price');
  return <Text>{title} — ₦{price}</Text>;
}
```

### New API (v6) — Receiving Parameters ✅

In v6+, params come through a separate `route` prop, not `navigation`:

```jsx
// ✅ NEW - DetailsScreen.js
import { View, Text } from 'react-native';

export default function DetailsScreen({ route, navigation }) {
  // Destructure directly from route.params
  const { title, price } = route.params;

  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text style={{ fontSize: 24 }}>{title}</Text>
      <Text>Price: ₦{price}</Text>
    </View>
  );
}
```

### Full Example — Home with Multiple Items

```jsx
// ✅ HomeScreen.js with multiple buttons
import { View, Text, Button } from 'react-native';

const items = [
  { id: 'first', title: 'First Item', price: 2500, stock: 1 },
  { id: 'second', title: 'Second Item', price: 5000, stock: 0 },
  { id: 'third', title: 'Third Item', price: 1200, stock: 200 },
];

export default function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text style={{ fontSize: 20, marginBottom: 20 }}>Home Screen</Text>
      {items.map(item => (
        <Button
          key={item.id}
          title={item.title}
          onPress={() => navigation.navigate('Details', item)}
        />
      ))}
    </View>
  );
}
```

---

## Part 3 — The Navigation Header

### Old API (v4) — Static Options

```jsx
// ❌ OLD (v4) — set as static property on the component
Home.navigationOptions = {
  title: 'Home',
};

// ❌ OLD (v4) — dynamic options using a function
Details.navigationOptions = ({ navigation }) => ({
  title: navigation.getParam('title'),
  headerRight: () => (
    <Button title="Buy" onPress={() => {}} />
  ),
});
```

### New API (v6) — Options Prop ✅

In v6, header options are defined on the `<Stack.Screen>` component itself:

**Static title:**

```jsx
// ✅ NEW - static title on Stack.Screen
<Stack.Screen
  name="Home"
  component={HomeScreen}
  options={{ title: 'Home' }}
/>
```

**Dynamic title from params:**

```jsx
// ✅ NEW - dynamic title using route
<Stack.Screen
  name="Details"
  component={DetailsScreen}
  options={({ route }) => ({
    title: route.params.title,
    headerRight: () => (
      <Button
        title="Buy"
        onPress={() => {}}
        disabled={route.params.stock === 0}
      />
    ),
  })}
/>
```

**Setting options from inside the screen component:**

```jsx
// ✅ NEW - you can also set options from inside the screen using useLayoutEffect
import { useLayoutEffect } from 'react';

export default function DetailsScreen({ route, navigation }) {
  const { title, stock } = route.params;

  useLayoutEffect(() => {
    navigation.setOptions({
      title: title,
      headerRight: () => (
        <Button title="Buy" disabled={stock === 0} onPress={() => {}} />
      ),
    });
  }, [navigation, title, stock]);

  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>{route.params.content}</Text>
    </View>
  );
}
```

---

## Part 4 — Tab and Drawer Navigation

### Old API (v4) — For Reference

```jsx
// ❌ OLD (v4)
import { createBottomTabNavigator } from 'react-navigation-tabs';
import { createDrawerNavigator } from 'react-navigation-drawer';
import { Platform } from 'react-native';

const { createNavigator } = Platform.select({
  ios: { createNavigator: createBottomTabNavigator },
  android: { createNavigator: createDrawerNavigator },
});

export default createAppContainer(
  createNavigator({ Home, News, Settings }, { initialRouteName: 'Home' })
);
```

### New API (v6) — Bottom Tabs ✅

```jsx
// ✅ NEW - Tab Navigation (v6)
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import HomeScreen from './screens/HomeScreen';
import NewsScreen from './screens/NewsScreen';
import SettingsScreen from './screens/SettingsScreen';

const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator initialRouteName="Home">
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="News" component={NewsScreen} />
        <Tab.Screen name="Settings" component={SettingsScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```

### New API (v6) — Drawer Navigation ✅

```jsx
// ✅ NEW - Drawer Navigation (v6)
import { NavigationContainer } from '@react-navigation/native';
import { createDrawerNavigator } from '@react-navigation/drawer';
import HomeScreen from './screens/HomeScreen';
import NewsScreen from './screens/NewsScreen';
import SettingsScreen from './screens/SettingsScreen';

const Drawer = createDrawerNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Drawer.Navigator initialRouteName="Home">
        <Drawer.Screen name="Home" component={HomeScreen} />
        <Drawer.Screen name="News" component={NewsScreen} />
        <Drawer.Screen name="Settings" component={SettingsScreen} />
      </Drawer.Navigator>
    </NavigationContainer>
  );
}
```

### Platform-Specific Navigation (Tabs on iOS, Drawer on Android) ✅

```jsx
// ✅ NEW - Platform aware navigation (v6)
import { Platform } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createDrawerNavigator } from '@react-navigation/drawer';
import HomeScreen from './screens/HomeScreen';
import NewsScreen from './screens/NewsScreen';
import SettingsScreen from './screens/SettingsScreen';

const Tab = createBottomTabNavigator();
const Drawer = createDrawerNavigator();

// Pick the right Navigator based on platform
const Navigator = Platform.OS === 'ios' ? Tab : Drawer;

export default function App() {
  return (
    <NavigationContainer>
      <Navigator.Navigator initialRouteName="Home">
        <Navigator.Screen name="Home" component={HomeScreen} />
        <Navigator.Screen name="News" component={NewsScreen} />
        <Navigator.Screen name="Settings" component={SettingsScreen} />
      </Navigator.Navigator>
    </NavigationContainer>
  );
}
```

---

## Part 5 — Handling State Across Screens

### The Problem

App-level state (like a shopping cart, or product stock levels) needs to be accessible
to multiple screens. The navigation system sits between `App` and your screens, which
makes passing state tricky.

### Old API (v4) — `screenProps`

```jsx
// ❌ OLD (v4) - passing state via screenProps
return <Nav screenProps={{ stock, updateStock }} />;

// In the screen component:
export default function DetailsScreen({ screenProps }) {
  const { stock, updateStock } = screenProps;
}
```

`screenProps` was removed in v5. It no longer exists.

### New API (v6) — React Context ✅

The modern approach is to use **React Context** to share state across screens.
This is the same pattern you may have used in web React.

```jsx
// ✅ NEW - StockContext.js
import { createContext, useContext, useState } from 'react';

const StockContext = createContext();

// Provider component wraps everything that needs access to stock
export function StockProvider({ children }) {
  const [stock, setStock] = useState({
    first: 1,
    second: 0,
    third: 200,
  });

  function updateStock(id) {
    setStock(prev => ({
      ...prev,
      [id]: prev[id] === 0 ? 0 : prev[id] - 1,
    }));
  }

  return (
    <StockContext.Provider value={{ stock, updateStock }}>
      {children}
    </StockContext.Provider>
  );
}

// Custom hook for consuming the context
export function useStock() {
  return useContext(StockContext);
}
```

```jsx
// ✅ NEW - App.js - wrap NavigationContainer with the Provider
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { StockProvider } from './StockContext';
import HomeScreen from './screens/HomeScreen';
import DetailsScreen from './screens/DetailsScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <StockProvider>
      <NavigationContainer>
        <Stack.Navigator initialRouteName="Home">
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen
            name="Details"
            component={DetailsScreen}
            options={({ route }) => ({ title: route.params.title })}
          />
        </Stack.Navigator>
      </NavigationContainer>
    </StockProvider>
  );
}
```

```jsx
// ✅ NEW - DetailsScreen.js - consume stock from context
import { View, Text, Button } from 'react-native';
import { useStock } from '../StockContext';

export default function DetailsScreen({ route }) {
  const { title, content, id } = route.params;
  const { stock, updateStock } = useStock();

  const currentStock = stock[id];

  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text style={{ fontSize: 20 }}>{title}</Text>
      <Text>{content}</Text>
      <Text>In stock: {currentStock}</Text>
      <Button
        title="Buy"
        onPress={() => updateStock(id)}
        disabled={currentStock === 0}
      />
    </View>
  );
}
```

> 📌 **TEACHER NOTE:** This is a great moment to connect back to what students
> already know from web React. "We already know Context. We're just using it
> here to solve the same problem — sharing state across components — except those
> components happen to be screens in a mobile app."

---

## Navigation Quick Reference

### The Three Navigator Types

| Navigator | Import | Use Case |
|---|---|---|
| Stack | `@react-navigation/native-stack` | Standard screen-to-screen (most common) |
| Bottom Tabs | `@react-navigation/bottom-tabs` | Tab bar at bottom (iOS style) |
| Drawer | `@react-navigation/drawer` | Side menu (Android style) |

### Key Navigation Methods

| Method | What It Does |
|---|---|
| `navigation.navigate('ScreenName')` | Go to a screen |
| `navigation.navigate('ScreenName', { key: value })` | Go to a screen with params |
| `navigation.goBack()` | Go back to previous screen |
| `navigation.push('ScreenName')` | Push screen even if already in stack |
| `navigation.replace('ScreenName')` | Replace current screen |
| `navigation.popToTop()` | Go all the way back to first screen |

### Accessing Route Params

```jsx
// Always destructure from route.params
export default function MyScreen({ route, navigation }) {
  const { id, title, price } = route.params;
}
```

---

## Chapter 16 Exercises

### Exercise 1 — Basic Stack Navigation
Create a two-screen app:
- **Home screen** with a button that says "See My Profile"
- **Profile screen** that shows some placeholder text about a person
- A back button that returns to Home

### Exercise 2 — Passing Parameters
Extend Exercise 1:
- On the Home screen, show a list of 3 names (as buttons)
- When a name is tapped, navigate to a Profile screen
- The Profile screen should display the name that was tapped

### Exercise 3 — Custom Header
Extend Exercise 2:
- Set the navigation header title dynamically based on the person's name
- Add a "Follow" button in the header right area

### Exercise 4 — Tab Navigation
Convert your app to use bottom tab navigation with three tabs:
- Home
- Search
- Profile

### Exercise 5 — Shared State (Advanced)
Build a simple shopping list app:
- A list screen that shows items with a count
- A detail screen for each item with a "Add to Cart" button
- Use React Context so the cart count is visible on the list screen even after navigating back

---

# Full Summary

## What We Covered

**Chapter 14 — Expo Setup**
- React Native is React, but targeting mobile native UI instead of the browser DOM
- Expo removes the painful native toolchain setup
- Current command: `npx create-expo-app my-project`
- Run with `npm start`, view on phone via Expo Go app and QR code
- Expo Snack for browser-based development

**Chapter 15 — Styling & Flexbox**
- Styles are JavaScript objects, not CSS files
- `StyleSheet.create()` is the standard way to define styles
- React Native uses Flexbox as its default layout system
- Every `<View>` is already a flex container
- `flexDirection`: main axis direction (`'column'` default, `'row'`)
- `justifyContent`: distribution along main axis
- `alignItems`: alignment along cross axis (all children)
- `alignSelf`: individual child overrides parent alignment
- `flex`: proportional space allocation
- `flexWrap`: wrapping into grid layouts
- Layouts can be nested: rows inside columns, columns inside rows

**Chapter 16 — React Navigation**
- React Navigation v4 API is deprecated — don't use it in new projects
- Current v6 uses `<NavigationContainer>`, `Stack.Navigator`, `Stack.Screen`
- Parameters pass via second arg of `navigate()` and received via `route.params`
- Header options defined on `<Stack.Screen options={...}>`
- Tab and Drawer navigators available, can be chosen per platform
- Shared state across screens → React Context (not `screenProps`)

## Key Imports Cheat Sheet

```jsx
// React Native core
import { View, Text, Button, StyleSheet, Platform, StatusBar } from 'react-native';

// Expo status bar
import { StatusBar } from 'expo-status-bar';

// Navigation
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createDrawerNavigator } from '@react-navigation/drawer';

// React hooks (same as web)
import { useState, useEffect, useContext, useLayoutEffect, createContext } from 'react';
```

---

*Document covers React Native Chapters 14, 15 & 16. Commands and APIs updated to
current standards as of 2024–2025. Old APIs included for reference only.*
