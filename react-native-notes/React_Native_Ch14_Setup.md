# Chapter 14 — Getting Started with React Native & Expo

> **For the teacher:** Teaching notes are marked with `📌 TEACHER NOTE`.
> Code blocks are ready to live-code. Exercises are at the end.
>
> **For the student (after class):** Read through section by section.
> Every concept has an explanation, an analogy, and working code.
> Try the exercises on your own.

---

## Table of Contents

- [What is React Native?](#what-is-react-native)
- [What is Expo?](#what-is-expo)
- [A Brief History of Setup Commands](#history)
- [Installing and Creating a Project](#installing)
- [Viewing Your App on Your Phone](#viewing)
- [Expo Snack — Browser Playground](#snack)
- [Exercises](#exercises)

---

<a name="what-is-react-native"></a>
## What is React Native?

You already know React. You know components, JSX, props, state, and hooks.
React Native is React — but instead of rendering to the browser DOM, it renders
to **native mobile UI components** on iOS and Android.

Think of it this way:

```
React (Web)              React Native (Mobile)
────────────────────     ──────────────────────────
<div>           →        <View>
<p>             →        <Text>
<img>           →        <Image>
<button>        →        <TouchableOpacity> or <Pressable>
<input>         →        <TextInput>
<ul> / <li>     →        <FlatList>
CSS files       →        StyleSheet (JavaScript objects)
React DOM       →        React Native runtime
```

The mental model is exactly the same. Components, props, state, hooks — all
identical. What changes is the **primitives** (the building block tags) and
the **styling system**.

> 📌 **TEACHER NOTE:** Spend a moment here connecting to what students already
> know. Say something like: "Everything you learned about React over the past
> weeks — you keep all of it. We're not starting from scratch. We're just
> swapping out the building blocks and learning how styling works differently."

---

### How React Native Renders to the Phone

React Native is not a web app wrapped in a browser. Your JavaScript code gets
translated into real, native UI components:

```
Your JavaScript / JSX Code
          ↓
  React Native Runtime
          ↓
  Bridge / JSI (translator)
          ↓
     Native Layer
┌──────────────┬──────────────┐
│     iOS      │   Android    │
│  (Swift /    │  (Kotlin /   │
│  Obj-C)      │    Java)     │
│              │              │
│  UIView      │  View        │
│  UILabel     │  TextView    │
│  UIButton    │  Button      │
└──────────────┴──────────────┘
```

When you write `<View>`, React Native doesn't draw a box itself. It tells the
native layer "create a native view" — and iOS renders a real `UIView` in Swift,
Android renders a real `View` in Kotlin. That's why the app feels like a real
native app, not a website.

---

<a name="what-is-expo"></a>
## What is Expo?

Setting up a raw React Native project without Expo requires:
- Xcode (for iOS — Mac only)
- Android Studio + Android SDK
- Java JDK
- Environment variables configured
- Emulators set up

That's a lot of work before writing a single line of code.

**Expo** removes all of that. It is a toolchain and platform built on top of
React Native that handles all the setup complexity for you. With Expo you can:

- Scaffold a new project in one command
- Run your app on a real phone without any build tools
- Access device features (camera, location, notifications) without native code
- Use Expo Snack for browser-based development

> 📌 **TEACHER NOTE:** A good analogy — Expo is to React Native what Vite is
> to web React. It is the recommended starting point that removes toolchain
> complexity so you can focus on building. Nobody sets up webpack from scratch
> to start a web React project. Nobody sets up raw React Native from scratch
> to start a mobile project.

---

<a name="history"></a>
## A Brief History of Setup Commands

> 📌 **TEACHER NOTE:** Cover this briefly so students aren't confused when they
> find older tutorials online that use different commands. It also explains
> why the slides you may have seen use outdated commands.

The way you create a new Expo project has changed several times:

```
2017 → create-react-native-app
         A thin wrapper around Expo. Deprecated because Expo
         already had its own CLI that did the same thing better.
         ↓
2018–2022 → expo-cli / expo init
         The official Expo global CLI tool. Also deprecated
         when Expo moved to a faster, simpler approach.
         ↓
2022–present → npx create-expo-app   ✅  USE THIS
         No global install needed. Always uses the latest version.
```

The **concept** has always been the same — generate a boilerplate project with
all the scaffolding ready. Only the command changed.

If you see an old tutorial using `create-react-native-app` or `expo init`,
know that those are deprecated. Use `npx create-expo-app` instead.

---

<a name="installing"></a>
## Installing and Creating a Project

### Step 1 — Check Node.js

Expo requires Node.js. Verify you have it:

```bash
node -v
# Should return v18.x.x or higher
```

Download from https://nodejs.org if needed.

---

### Step 2 — Create a New Project

No global installation needed. Run this one command:

```bash
npx create-expo-app my-project --template blank
```

The `--template blank` flag gives you a JavaScript (JSX) project.
Without it you may get TypeScript by default.

> 📌 **TEACHER NOTE:** Always use `--template blank` in class to ensure
> everyone gets JavaScript/JSX. TypeScript is great but it adds syntax
> overhead that's unnecessary when learning React Native fundamentals.

You will see Expo scaffold the project:

```
✔ Downloaded and extracted project files
✔ Installed JavaScript dependencies

✅ Your project is ready!

To run your project, navigate to the directory and run one of the
following npm commands.

- cd my-project
- npm run android
- npm run ios
- npm run web
- npm start
```

---

### Step 3 — What Gets Created

```
my-project/
│
├── assets/
│   ├── adaptive-icon.png    ← Android home screen icon
│   ├── favicon.png          ← Browser tab icon (web mode)
│   ├── icon.png             ← Main app icon (iOS)
│   └── splash-icon.png      ← Loading/splash screen image
│
├── .gitignore               ← Tells Git what to ignore
├── App.js                   ← YOUR ROOT COMPONENT — start here
├── app.json                 ← Expo configuration (name, icon, etc.)
├── index.js                 ← Entry point (rarely touched)
├── package.json             ← Dependencies and scripts
└── package-lock.json        ← Auto-generated, never edit manually
```

The two files you work in most are:
- `App.js` — your root component, the starting point of your UI
- `app.json` — your app's identity and configuration

---

### Step 4 — Start the Development Server

```bash
cd my-project
npm start
```

This launches **Metro** — the JavaScript bundler React Native uses (similar to
Vite/Webpack but for mobile). You will see:

```
Starting Metro Bundler

› Metro waiting on exp://192.168.x.x:8081

› Press a │ open Android
› Press i │ open iOS simulator
› Press w │ open web

› Press r │ reload app
› Press m │ toggle menu
› Press ? │ show all commands
```

> 📌 **TEACHER NOTE:** Show the class all the keyboard shortcuts. Press `w`
> to show the web preview quickly. Explain that web preview is a convenience
> tool for quick checks — the real target is always the phone.

---

### Step 5 — Understanding the Boilerplate `App.js`

Open `App.js`. This is what you start with:

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

Key things to notice:

| What you see | What it means |
|---|---|
| `View` instead of `div` | The basic container in React Native |
| `Text` instead of `p` | All text MUST be inside a `<Text>` component |
| `StyleSheet.create({...})` | No CSS files — styles are JavaScript objects |
| `style={styles.container}` | No `className` — use the `style` prop directly |
| `flex: 1` on container | Tells the View to fill the entire screen |

> 📌 **TEACHER NOTE:** The `<Text>` rule is important to reinforce. In web
> React you can put raw text anywhere. In React Native, any text that is not
> inside a `<Text>` component will throw an error. This catches students off
> guard the first time.

---

<a name="viewing"></a>
## Viewing Your App on Your Phone

### Option A — Expo Go App (Recommended for Development)

1. Install **Expo Go** on your phone
   - Android: Google Play Store → search "Expo Go"
   - iOS: App Store → search "Expo Go"

2. Make sure your phone and laptop are on the **same Wi-Fi network**

3. Open Expo Go and tap **Scan QR Code**

4. Point your camera at the QR code shown in the terminal

5. Your app loads on your phone — live and hot-reloading

> 📌 **TEACHER NOTE:** The "same Wi-Fi network" requirement is the most common
> issue in classroom setups. School/office networks sometimes block
> device-to-device communication. If QR scanning fails, use tunnel mode.

---

### Option B — Tunnel Mode (When Same Wi-Fi Doesn't Work)

First install ngrok globally — you only need to do this once:

```bash
npm install -g @expo/ngrok@^4.1.0
```

Then start with tunnel:

```bash
npx expo start --tunnel
```

Tunnel routes traffic through an external server so your phone and laptop
don't need to be on the same network:

```
Without tunnel (LAN):
Phone ──── Wi-Fi ──── Your Laptop
           (must be same network)

With tunnel:
Phone ──── Internet ──── ngrok server ──── Your Laptop
           (works from anywhere)
```

The tradeoff is speed — tunnel is slower than direct LAN. Use LAN when it
works, tunnel when it doesn't.

---

### Looking at App.js — The Entry Point Chain

```
index.js                          ← first file that runs
  └── registerRootComponent(App)  ← registers App with React Native
        └── App.js                ← your root component
              └── all your screens and components
```

**Web React equivalent:**

```
Web React (Vite)             React Native (Expo)
────────────────────         ────────────────────────────
main.jsx                 →   index.js
  ReactDOM.createRoot()  →     registerRootComponent(App)
  .render(<App />)
```

---

<a name="snack"></a>
## Expo Snack — Browser-Based Playground

Expo Snack is an online IDE for React Native. No installation, no phone needed.

Visit: **https://snack.expo.dev**

What you can do:
- Write and run React Native code directly in the browser
- Switch between Android, iOS, and Web previews
- Import a GitHub repository
- Share your code with a link

> 📌 **TEACHER NOTE:** Expo Snack is your classroom safety net. If a student's
> local setup breaks mid-class, they can open snack.expo.dev on any browser and
> continue following along immediately. No time wasted debugging setup issues
> during teaching time.
>
> Also good for quickly demonstrating a concept without switching projects.

---

## Key Differences: Web React vs React Native

| Concept | Web React | React Native |
|---|---|---|
| Entry point | `main.jsx` | `index.js` |
| Root component | `App.jsx` | `App.js` |
| Configuration | `vite.config.js` | `app.json` |
| Container element | `<div>` | `<View>` |
| Text element | `<p>`, `<span>`, `<h1>` | `<Text>` (all text) |
| Images | `<img>` | `<Image>` |
| Input | `<input>` | `<TextInput>` |
| Styling | CSS / CSS Modules | `StyleSheet.create()` |
| Applying styles | `className` | `style` prop |
| HTML shell | `index.html` | None — no HTML in React Native |
| Bundler | Vite / Webpack | Metro |

The biggest shift: **there is no HTML**. React Native renders directly to
native UI. The JavaScript IS the interface.

---

<a name="exercises"></a>
## Chapter 14 Exercises

### Exercise 1 — Your First Screen
Modify `App.js` to display your name and a short bio. Use at least two
`<Text>` components and give each one a different style (different font
size, color, or font weight).

### Exercise 2 — Explore `app.json`
Open `app.json` and change the `name` field to something else.
Reload the app and see if anything changes visually.
Then change it back. What does this field actually control?

### Exercise 3 — Break and Fix
Deliberately remove `flex: 1` from the container style. What happens to
the layout? Now add it back. Explain in your own words what `flex: 1` does.

### Exercise 4 — Expo Snack
Go to https://snack.expo.dev and build a component that displays:
- A heading: "My First React Native App"
- Your name below it
- A favourite quote below that
Run it on both the iOS and Android virtual devices and note any differences.

---

*Chapter 14 — React Native & Expo Setup.*
*Commands updated to current standards (2024–2025).*
*`create-react-native-app` and `expo init` are deprecated — use `npx create-expo-app`.*
