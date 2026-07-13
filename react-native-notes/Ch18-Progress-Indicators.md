# Chapter 18: Progress Indicators in React Native

## 1. Why Progress Indicators Matter

Imagine a microwave oven with no window and no sound — the only way to interact with it is a button labeled *Cook*. You press it, and... nothing. Is it working? Is it done? Did it even turn on?

That's exactly what many software users face when an app doesn't show progress. They tap a button and stare at a blank screen, unsure if anything is happening at all.

React Native gives us components and patterns for closing that gap — some for **indeterminate** feedback ("something is happening, I just don't know how long"), and some for **precise** feedback ("you're 60% done").

> 💡 **Tip:** Whenever you're about to build a screen that waits on something — a network call, a file upload, a multi-step form — ask yourself: "What would the microwave-with-no-window version of this look like?" If the answer is unsettling, you need a progress indicator.

---

## 2. When Do You Need One?

Use this as a quick decision guide when you're building a screen:

| Situation | Use |
|---|---|
| You don't know how long a task will take (e.g., waiting for an API response with no size/duration info) | **ActivityIndicator** (spinner) |
| You know the task's completion can be measured as a percentage (e.g., a file upload, a download) | **ProgressBar** |
| The user is navigating to a screen that needs to fetch data before it can render | **Navigation-based loading indicator** |
| The user is moving through a fixed number of steps (e.g., a multi-page form, onboarding flow, checkout) | **Step progress indicator** |

> 💡 **Tip:** If you're not sure whether something is "indeterminate" or "measurable," ask: *can I calculate a percentage right now?* If yes → ProgressBar. If no → ActivityIndicator.

---

## 3. The Four Types at a Glance

| Type | What It Shows | Best For |
|---|---|---|
| **ActivityIndicator** | A spinning animation, no specific value | Short waits, unknown duration, general "loading" states |
| **ProgressBar** | A filling bar tied to a 0–1 value | Uploads, downloads, anything with a measurable completion percentage |
| **Navigation Loading Indicator** | A spinner shown while a screen's data loads, tied to navigation | Screens that fetch data as soon as the user navigates to them |
| **Step Progress** | A bar or indicator showing position within a sequence | Multi-step forms, wizards, onboarding flows |

---

## 4. Deep Dive: The Four Types

### 4.1 Indicating Progress — `ActivityIndicator`

`ActivityIndicator` is React Native's built-in, platform-agnostic spinner. You render it when something is happening but you don't have (or need) a precise progress value.

```jsx
import React from "react";
import { View, ActivityIndicator } from "react-native";
import styles from "./styles";

export default function App() {
  return (
    <View style={styles.container}>
      <ActivityIndicator size="large" />
    </View>
  );
}
```

It automatically renders the native-looking spinner for each platform — the classic iOS spinner on iOS, and Android's circular spinner on Android — while communicating the same thing on both: *you're waiting for something.*

You can also use `size="small"` when embedding the indicator inside a smaller element, like a button.

**Real-world use case:** A login button that shows a small spinner in place of its label while an authentication request is in flight.

**Other options that exist:**
- `react-native-paper`'s `ActivityIndicator` — same idea, themeable to match a Material Design UI.
- A custom Lottie animation (via `lottie-react-native`) if you want a branded loading animation instead of the native spinner.

---

### 4.2 Measuring Progress — `ProgressBar`

Unlike `ActivityIndicator`, there is **no platform-agnostic progress bar built into React Native anymore** — `ProgressViewIOS` and `ProgressBarAndroid` were removed from core React Native and are no longer maintained as part of the main library. So we build our own.

**Option A — React's own building block: a custom `View`-based bar**

This is the most "fundamentals-first" approach — no extra dependency, just a `View` whose width is driven by state.

```jsx
// ProgressBar.js
import React from "react";
import PropTypes from "prop-types";
import { View, Text } from "react-native";
import styles from "./styles";

export default function ProgressBar({ progress, label }) {
  return (
    <View style={styles.progressWrapper}>
      {label && (
        <Text style={styles.progressText}>
          {Math.round(progress * 100)}%
        </Text>
      )}
      <View style={styles.track}>
        <View style={[styles.fill, { width: `${progress * 100}%` }]} />
      </View>
    </View>
  );
}

ProgressBar.propTypes = {
  progress: PropTypes.number.isRequired,
  label: PropTypes.bool.isRequired
};

ProgressBar.defaultProps = {
  progress: 0,
  label: true
};
```

```jsx
// styles.js (relevant excerpt)
export default {
  progressWrapper: { width: "100%", alignItems: "center" },
  track: {
    width: "100%",
    height: 8,
    backgroundColor: "#e0e0e0",
    borderRadius: 4,
    overflow: "hidden"
  },
  fill: {
    height: "100%",
    backgroundColor: "#2e86de"
  },
  progressText: { marginBottom: 4, fontSize: 12, color: "#555" }
};
```

Using it, with a simulated task via `useState`/`useEffect`:

```jsx
import React, { useState, useEffect } from "react";
import { View } from "react-native";
import styles from "./styles";
import ProgressBar from "./ProgressBar";

export default function MeasuringProgress() {
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    function updateProgress() {
      setProgress(currentProgress => {
        if (currentProgress < 1) {
          setTimeout(updateProgress, 300);
        }
        return currentProgress + 0.01;
      });
    }
    updateProgress();
  }, []);

  return (
    <View style={styles.container}>
      <ProgressBar progress={progress} />
    </View>
  );
}
```

The `progress` prop takes a value between `0` and `1`; the bar's fill width and the percentage label both derive from it.

**Real-world use case:** Showing upload percentage while a user uploads a profile photo or document.

**Other options that exist:**
- **`react-native-progress`** — a popular community package offering `Bar`, `Circle`, and `Pie` progress components, animated out of the box.
- **`@react-native-community/progress-bar-android`** and **`@react-native-community/progress-view`** — the community-maintained continuations of the old core `ProgressBarAndroid`/`ProgressViewIOS`, if you specifically want native platform-styled bars.

---

### 4.3 Navigation Indicators

Some screens need to fetch data *before* they're useful to show — so we want a loading spinner to appear the moment the user navigates there, and disappear once the data arrives.

With React Navigation v6, the cleanest approach is to handle loading state **inside the screen component itself**, rather than through route params (as older React Navigation versions did).

```jsx
// useLoading.js — a small custom hook for the loading pattern
import { useState, useEffect } from "react";

export default function useLoading(fetchFn) {
  const [loading, setLoading] = useState(true);
  const [data, setData] = useState(null);

  useEffect(() => {
    let isMounted = true;
    fetchFn()
      .then(result => {
        if (isMounted) {
          setData(result);
          setLoading(false);
        }
      })
      .catch(() => {
        if (isMounted) setLoading(false);
      });
    return () => { isMounted = false; };
  }, []);

  return { loading, data };
}
```

```jsx
// FirstScreen.js
import React from "react";
import { View, ActivityIndicator, Text } from "react-native";
import styles from "./styles";
import useLoading from "./useLoading";
import { fetchScreenData } from "./api";

export default function FirstScreen({ navigation }) {
  const { loading, data } = useLoading(fetchScreenData);

  if (loading) {
    return (
      <View style={styles.container}>
        <ActivityIndicator size="large" />
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <Text style={styles.content}>{data.title}</Text>
      <Text onPress={() => navigation.navigate("Second")}>
        Go to Second
      </Text>
    </View>
  );
}
```

Because `useLoading` re-runs its effect each time the component mounts, navigating to the screen fresh (e.g., via `navigation.navigate`) naturally re-triggers the fetch and the loading state — matching the original goal of fetching fresh data and showing a spinner every time the user lands on the screen.

**Real-world use case:** A product detail screen that fetches the product's full data from an API as soon as the user taps into it from a list.

**Other options that exist:**
- **React Query** (`@tanstack/react-query`) or **SWR** — data-fetching libraries that manage loading/error/cache state for you, so you get `isLoading` for free without writing a custom hook.
- **React Navigation's `screenOptions`/`options`** with a custom `headerRight`/`headerTitle` — useful if you want the loading indicator to appear in the header itself rather than the screen body.

---

### 4.4 Step Progress

For multi-step flows (forms, onboarding, checkout), it helps to show the user *where they are* in a fixed sequence of steps — reusing the same `ProgressBar` component from section 4.2, but driven by step position instead of a fetch/upload percentage.

```jsx
// App.js — React Navigation v6 native stack
import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";
import First from "./First";
import Second from "./Second";
import Third from "./Third";
import Fourth from "./Fourth";

const Stack = createNativeStackNavigator();
const routeNames = ["First", "Second", "Third", "Fourth"];

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="First">
        <Stack.Screen name="First" component={First} />
        <Stack.Screen name="Second" component={Second} />
        <Stack.Screen name="Third" component={Third} />
        <Stack.Screen name="Fourth" component={Fourth} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export function stepProgress(routeName) {
  return (routeNames.indexOf(routeName) + 1) / routeNames.length;
}
```

```jsx
// First.js
import React, { useLayoutEffect } from "react";
import { View, Text } from "react-native";
import styles from "./styles";
import ProgressBar from "./ProgressBar";
import { stepProgress } from "./App";

export default function First({ navigation, route }) {
  useLayoutEffect(() => {
    navigation.setOptions({
      headerTitle: () => (
        <View style={styles.progressHeader}>
          <Text style={styles.title}>First</Text>
          <ProgressBar label={false} progress={stepProgress(route.name)} />
        </View>
      )
    });
  }, [navigation, route.name]);

  return (
    <View style={styles.container}>
      <Text style={styles.content}>First Content</Text>
      <Text onPress={() => navigation.navigate("Second")}>Next: Second</Text>
    </View>
  );
}
```

Each screen calculates its own position using `stepProgress(route.name)` and passes it into the same `ProgressBar` component used for measuring progress — so the bar fills up as the user advances through First → Second → Third → Fourth.

**Real-world use case:** A checkout flow (Cart → Shipping → Payment → Confirm) that shows a progress bar at the top so users know how many steps remain.

**Other options that exist:**
- **`react-native-step-indicator`** — a dedicated community package for dot/step-based indicators (rather than a continuous bar), often better suited to onboarding screens.
- **React Navigation's built-in `progress` value** — newer stack navigators expose animated progress values through `useHeaderHeight`/gesture APIs, but these are meant for transition animations, not user-facing step indicators, so a custom bar (as above) is still the right tool here.

---

## 5. Exercises

1. **ActivityIndicator:** Add a small `ActivityIndicator` to a submit button. It should appear only while `isSubmitting` is `true`, replacing the button's text.
2. **ProgressBar:** Simulate a file download using `useState`/`useEffect` and `setTimeout` (as in section 4.2), and render the custom `ProgressBar` component to show its progress from 0% to 100%.
3. **Navigation Indicator:** Build a two-screen app where the second screen shows a spinner for 2 seconds (simulating an API call) before displaying "Data loaded!".
4. **Step Progress:** Extend the four-screen stack example into a 3-step "Sign Up" flow (Account Info → Preferences → Confirm), with the step progress bar shown in each screen's header.
5. **Challenge:** Combine two patterns — build a 3-step form where each step also shows an `ActivityIndicator` while "saving" that step's data before advancing to the next.

---

## 6. Summary

- Progress indicators exist to solve one problem: users shouldn't be left wondering if anything is happening.
- Use **ActivityIndicator** when you don't know how long something will take.
- Use **ProgressBar** when you can calculate a percentage — built from scratch with a `View`, or via packages like `react-native-progress`.
- Use a **navigation-based loading pattern** when a screen needs to fetch data as soon as the user arrives at it.
- Use **step progress** when guiding a user through a fixed sequence, reusing the same `ProgressBar` component driven by step position instead of a task percentage.
