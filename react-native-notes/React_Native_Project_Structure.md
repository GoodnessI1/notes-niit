# React Native (Expo) — Project Structure Explained
### Based on a real `npx create-expo-app --template blank` project

> This document walks through every file and folder in a freshly created
> React Native Expo project and explains what it does, why it exists,
> and whether you need to touch it or not.

---

## Visual Map

```
rn-test-jsx/
│
├── .claude/
│   └── settings.json
│
├── assets/
│   ├── adaptive-icon.png
│   ├── favicon.png
│   ├── icon.png
│   └── splash-icon.png
│
├── .gitignore
├── AGENTS.md
├── App.js               ← YOU WORK HERE
├── app.json
├── CLAUDE.md
├── index.js
├── package.json
└── package-lock.json
```

---

## File by File Breakdown

---

### `App.js` ← Your Starting Point

**Touch it? YES — this is where you write your app.**

This is the **root component** of your entire application. Everything your app
displays starts here. Think of it the same way you think of `App.jsx` in a
web React project — it is the top of the component tree.

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

As your app grows, you won't keep everything in `App.js`. You'll create a
`screens/` folder and a `components/` folder and import them here. But
`App.js` always stays as the root.

> 📌 **TEACHER NOTE:** Compare directly to what students already know.
> In web React (Vite), the root was `App.jsx`. Same idea, same role.
> The only difference is the component tree now renders to a mobile screen
> instead of a browser window.

---

### `index.js` — The True Entry Point

**Touch it? Almost never.**

If `App.js` is the root component, `index.js` is what **registers** that root
component with the React Native runtime. It is the very first JavaScript file
that runs when your app launches.

```js
import { registerRootComponent } from 'expo';
import App from './App';

// registerRootComponent calls AppRegistry.registerComponent('main', () => App);
// It also ensures that whether you load the app in Expo Go or in a native build,
// the environment is set up appropriately.
registerRootComponent(App);
```

You generally never need to edit this file. The only reason you would is if you
were doing very advanced setup like integrating a third-party navigation library
at the root level before `App` mounts — which is an edge case.

**Web React equivalent:** Think of this as `main.jsx` in a Vite project —
the file that calls `ReactDOM.createRoot(...).render(<App />)`.

```
Web React (Vite)             React Native (Expo)
────────────────────         ────────────────────────────
main.jsx                 →   index.js
  ReactDOM.createRoot()  →     registerRootComponent(App)
  .render(<App />)
```

---

### `app.json` — Expo Configuration

**Touch it? YES — when you need to configure your app's identity and appearance.**

This file controls how Expo builds and presents your app. It is not JavaScript —
it is a JSON configuration file that Expo reads.

```json
{
  "expo": {
    "name": "rn-test-jsx",
    "slug": "rn-test-jsx",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "userInterfaceStyle": "light",
    "splash": {
      "image": "./assets/splash-icon.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "ios": {
      "supportsTablet": true
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#ffffff"
      }
    },
    "web": {
      "favicon": "./assets/favicon.png"
    }
  }
}
```

Key fields explained:

| Field | What It Does |
|---|---|
| `name` | The app name shown under the icon on the home screen |
| `slug` | URL-friendly identifier used by Expo services |
| `version` | Your app version (relevant when publishing to app stores) |
| `orientation` | Lock to `portrait`, `landscape`, or `default` (both) |
| `icon` | The app icon image file |
| `splash` | The splash/loading screen shown while the app boots |
| `ios` / `android` | Platform-specific configuration |

> 📌 **TEACHER NOTE:** A good analogy — `app.json` is like the `<head>` section
> of an HTML file. You don't see it in the UI but it controls the identity,
> metadata, and appearance settings of your app at a configuration level.

---

### `assets/` — Static Files

**Touch it? YES — replace or add images, icons, fonts here.**

This folder holds static files that your app uses. Out of the box you get four
image files:

```
assets/
├── adaptive-icon.png    ← Android-specific icon (used on home screen)
├── favicon.png          ← Icon when running as a web app in a browser
├── icon.png             ← The main app icon (iOS + general use)
└── splash-icon.png      ← The image shown on the splash/loading screen
```

#### Why multiple icons?

Different platforms and contexts need different icon formats:

```
icon.png            → iOS home screen, general default icon
adaptive-icon.png   → Android adaptive icons (can be masked into different
                       shapes by the Android OS — circle, square, etc.)
favicon.png         → Shows in the browser tab when running as a web app
splash-icon.png     → What users see for 1-2 seconds while the JS loads
```

As you build your app you will add more assets here — images for your UI,
custom fonts, audio files, etc.

> 📌 **TEACHER NOTE:** The icon sizes matter for app store submission. Expo
> handles the resizing automatically from the source files you provide.
> For now in development, the defaults are fine.

---

### `package.json` — Project Dependencies and Scripts

**Touch it? SOMETIMES — when you add or remove packages.**

This is the same `package.json` you know from web React projects. It lists:
- Your project metadata (name, version)
- Your **dependencies** (packages your app needs to run)
- Your **devDependencies** (packages only needed during development)
- Your **scripts** (commands like `npm start`)

A typical Expo blank project `package.json` looks like:

```json
{
  "name": "rn-test-jsx",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  },
  "dependencies": {
    "expo": "~51.0.0",
    "expo-status-bar": "~1.12.1",
    "react": "18.2.0",
    "react-native": "0.74.1"
  },
  "devDependencies": {
    "@babel/core": "^7.24.0"
  }
}
```

Notice the `"main": "index.js"` field — this tells the runtime which file to
start from. That's why `index.js` is the entry point.

**Common scripts:**

```bash
npm start          # Start the Metro bundler (main dev command)
npm run android    # Start and open on Android emulator/device
npm run ios        # Start and open on iOS simulator (Mac only)
npm run web        # Start and open in the browser
```

---

### `package-lock.json` — Dependency Lock File

**Touch it? NEVER manually.**

This file is auto-generated by npm. It records the **exact version** of every
package installed (including the dependencies of your dependencies). It ensures
that everyone on your team gets identical package versions.

```
You:     npm install  →  reads package.json, installs, writes package-lock.json
Teammate: npm install →  reads package-lock.json, gets EXACT same versions
```

Commit this file to Git. Never edit it by hand.

**Web React equivalent:** Exactly the same as in Vite/CRA projects. Same file,
same purpose.

---

### `.gitignore` — Files Git Should Ignore

**Touch it? RARELY — only if you need to add custom ignore rules.**

This file tells Git which files and folders NOT to track or commit.
Out of the box it ignores:

```
node_modules/       ← too large, reinstalled via npm install
.expo/              ← Expo's local cache, machine-specific
dist/               ← build output
*.log               ← log files
```

You never commit `node_modules` because it can contain hundreds of megabytes.
Anyone who clones your repo just runs `npm install` and gets them back.

---

### `.claude/settings.json` — Claude Code Configuration

**Touch it? Only if you use Claude Code.**

This folder and file are created by **Claude Code** — Anthropic's AI coding
assistant that works in the terminal. If you have Claude Code installed and
active in this project, it uses `settings.json` to store project-specific
settings and permissions.

```json
// .claude/settings.json (example)
{
  "permissions": {
    "allow": ["read", "write"],
    "deny": []
  }
}
```

If you are not actively using Claude Code in this project, you can ignore
this folder entirely. It does not affect how your app runs.

---

### `CLAUDE.md` — Claude Code Instructions

**Touch it? Only if you use Claude Code.**

This is a special markdown file that Claude Code reads when it opens your
project. It acts like a "briefing document" — you write instructions, context,
and rules in here and Claude Code follows them when helping you in this project.

Example use cases:
```md
# CLAUDE.md

## Project Context
This is a React Native app for a cashless barter marketplace.

## Coding Conventions
- Use functional components only
- All styles go in StyleSheet.create()
- Folder structure: screens/, components/, context/

## Do Not
- Use class components
- Install packages without asking
```

Think of it as your AI pair programmer's onboarding document for this specific
project. If you're not using Claude Code interactively, this file has no effect
on your app.

---

### `AGENTS.md` — AI Agent Instructions

**Touch it? Only if you use AI coding agents.**

Similar to `CLAUDE.md`, this file provides instructions for AI coding agents
(not just Claude, but any agent tooling that follows the AGENTS.md convention).
It is a growing standard in the developer community for configuring how AI
assistants should behave in a given codebase.

If you are not using any AI agent tooling, this file has no effect on your app.

---

## Files You Will Create as Your App Grows

A blank project gives you the skeleton. As you build, your structure will
naturally evolve to look something like this:

```
rn-test-jsx/
│
├── assets/
│
├── components/           ← Reusable UI pieces
│   ├── Button.js
│   ├── Card.js
│   └── Header.js
│
├── screens/              ← One file per screen/page
│   ├── HomeScreen.js
│   ├── DetailsScreen.js
│   └── SettingsScreen.js
│
├── context/              ← React Context for shared state
│   └── AppContext.js
│
├── navigation/           ← Navigation setup
│   └── AppNavigator.js
│
├── App.js                ← Imports AppNavigator, wraps with providers
├── index.js
├── app.json
└── package.json
```

> 📌 **TEACHER NOTE:** React Native does not enforce any folder structure.
> This pattern above is a community convention, not a requirement. The same
> way web React projects vary in structure, so do React Native projects.
> What matters is consistency within your own project.

---

## Quick Reference — Do I Touch This File?

| File / Folder | Touch It? | When |
|---|---|---|
| `App.js` | ✅ YES | Always — your main working file |
| `index.js` | ⛔ RARELY | Almost never |
| `app.json` | ✅ YES | App name, icon, splash, orientation |
| `assets/` | ✅ YES | When adding images, icons, fonts |
| `package.json` | ✅ SOMETIMES | When installing/removing packages |
| `package-lock.json` | ⛔ NEVER | Auto-managed by npm |
| `.gitignore` | ⛔ RARELY | Only to add custom ignore rules |
| `.claude/settings.json` | ⛔ ONLY IF using Claude Code | |
| `CLAUDE.md` | ⛔ ONLY IF using Claude Code | |
| `AGENTS.md` | ⛔ ONLY IF using AI agents | |

---

## How It Compares to Web React (Vite)

Since you came from web React, here is a direct comparison:

| Role | Web React (Vite) | React Native (Expo) |
|---|---|---|
| Entry point | `main.jsx` | `index.js` |
| Root component | `App.jsx` | `App.js` |
| App configuration | `vite.config.js` | `app.json` |
| Static assets | `public/` | `assets/` |
| Dependencies | `package.json` | `package.json` (same) |
| Styles | CSS / CSS Modules | `StyleSheet.create()` in JS |
| HTML shell | `index.html` | **None** — no HTML in React Native |

The biggest conceptual shift: **there is no HTML file**. React Native renders
directly to native UI components. The JavaScript IS the interface.

---

*Document based on a project created with `npx create-expo-app --template blank`*
*React Native / Expo — 2024–2025*
