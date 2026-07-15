# Chapter 21: Alerts, Notifications, and Modals in React Native

This chapter covers how to communicate important information to the user — from things that must be acknowledged before continuing, to passive messages that fade away on their own.

---

## 1. Important Information

Before implementing anything, it helps to define the vocabulary, since these terms get used loosely:

- **Alert:** Something important just happened, and you need to ensure the user sees it. The user may need to acknowledge it before continuing.
- **Notification:** Something happened, but it's not important enough to block what the user is doing. These typically disappear on their own.
- **Confirmation:** A type of alert. If the user just performed an action and you want them to acknowledge the outcome (or confirm they want to proceed) before continuing, that's a confirmation.

> 💡 **Tip:** Use confirmations only when the workflow genuinely cannot continue without the user acknowledging what's going on. Overusing blocking alerts for minor things trains users to dismiss them without reading — the opposite of what you want.

---

## 2. Getting User Confirmation

There are two ways to get a confirmation in front of the user: React Native's built-in `Alert` API for quick, simple cases, and a custom `Modal`-based component when you need full control over styling and layout.

### 2.1 Quick Confirmations — `Alert.alert()`

For a simple "Are you sure?" dialog, React Native ships a built-in imperative API that needs no custom component at all:

```jsx
import { Alert } from "react-native";

function confirmDelete(onConfirm) {
  Alert.alert(
    "Delete this item?",
    "This action cannot be undone.",
    [
      { text: "Cancel", style: "cancel" },
      { text: "Delete", style: "destructive", onPress: onConfirm }
    ]
  );
}
```

`Alert.alert(title, message, buttons)` renders a native-styled dialog on both iOS and Android — no styling work required. The `buttons` array supports a `style` of `"default"`, `"cancel"`, or `"destructive"` (destructive renders in red on iOS).

**Real-world use case:** Confirming a destructive action like deleting an account or removing an item from a cart — the exact scenario shown above.

**When to reach for `Alert.alert()` instead of a custom Modal:** whenever the content is plain text with one or two buttons, and you don't need custom colors, images, or layout. It's faster to write and automatically matches the platform's native look.

### 2.2 Custom-Styled Confirmations — `Modal`

When you need more control over appearance — custom colors, icons, layout — build your own component around React Native's `<Modal>`.

**Success confirmation:**

```jsx
// ConfirmationModal.js
import React from "react";
import { View, Text, Modal } from "react-native";
import styles from "./styles";

export default function ConfirmationModal({
  visible,
  onPressConfirm,
  onPressCancel,
  transparent = true,
  onRequestClose = () => {}
}) {
  return (
    <Modal visible={visible} transparent={transparent} onRequestClose={onRequestClose}>
      <View style={styles.modalContainer}>
        <View style={styles.modalInner}>
          <Text style={styles.modalText}>Dude, srsly?</Text>
          <Text style={styles.modalButton} onPress={onPressConfirm}>Yep</Text>
          <Text style={styles.modalButton} onPress={onPressCancel}>Nope</Text>
        </View>
      </View>
    </Modal>
  );
}
```

**Error confirmation**, reusing most of the same styles as the success modal, layered with error-specific overrides:

```jsx
// ErrorModal.js
import React from "react";
import { View, Text, Modal } from "react-native";
import styles from "./styles";

const innerViewStyle = [styles.modalInner, styles.modalInnerError];
const textStyle = [styles.modalText, styles.modalTextError];
const buttonStyle = [styles.modalButton, styles.modalButtonError];

export default function ErrorModal({
  visible,
  onPressConfirm,
  onPressCancel,
  transparent = true,
  onRequestClose = () => {}
}) {
  return (
    <Modal visible={visible} transparent={transparent} onRequestClose={onRequestClose}>
      <View style={styles.modalContainer}>
        <View style={innerViewStyle}>
          <Text style={textStyle}>Epic fail!</Text>
          <Text style={buttonStyle} onPress={onPressConfirm}>Fix it</Text>
          <Text style={buttonStyle} onPress={onPressCancel}>Ignore it</Text>
        </View>
      </View>
    </Modal>
  );
}
```

```jsx
// styles.js
import { StyleSheet } from "react-native";

export default StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "ghostwhite"
  },
  modalContainer: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  },
  modalInner: {
    backgroundColor: "azure",
    padding: 20,
    borderWidth: 1,
    borderColor: "lightsteelblue",
    borderRadius: 2,
    alignItems: "center"
  },
  modalInnerError: {
    backgroundColor: "lightcoral",
    borderColor: "darkred"
  },
  modalText: {
    fontSize: 16,
    margin: 5,
    color: "slategrey"
  },
  modalTextError: {
    fontSize: 18,
    color: "darkred"
  },
  modalButton: {
    fontWeight: "bold",
    margin: 5,
    color: "slategrey"
  },
  modalButtonError: {
    color: "black"
  }
});
```

Notice how the error styles are combined as **arrays**: `[styles.modalInner, styles.modalInnerError]`. React Native merges style objects passed as an array left to right, so the error-specific style always comes last — that way, conflicting properties like `backgroundColor` are correctly overridden by the error variant.

**Real-world use case:** A checkout flow showing a custom-branded success modal ("Order placed! 🎉") with your app's colors and an icon, rather than the plain system-styled `Alert.alert()`.

**Other options that exist:**
- **`react-native-modal`** — a popular wrapper around core `Modal` adding built-in animations (slide, fade, zoom) without writing your own `Animated` logic.
- **`react-native-paper`'s `Dialog`** — Material Design themed, useful if the rest of your app already uses that design system.

---

## 3. Passive Notifications

Alerts and confirmations all require the user to take action. For things that are worth mentioning but not worth interrupting the user over, use a passive notification — one that appears briefly and disappears on its own without needing to be dismissed.

React Native's `ToastAndroid` API only works on Android. Rather than leaving iOS unhandled, here's a small cross-platform toast component you can build yourself.

```jsx
// Toast.js
import React, { useEffect, useRef } from "react";
import { Animated, Text, StyleSheet, Platform, ToastAndroid } from "react-native";

export default function Toast({ message, duration = 2000, visible }) {
  const opacity = useRef(new Animated.Value(0)).current;

  useEffect(() => {
    if (!message) return;

    if (Platform.OS === "android") {
      // On Android, defer to the native Toast — it's the platform convention.
      ToastAndroid.show(message, ToastAndroid.SHORT);
      return;
    }

    // On iOS (and web), fade a custom view in and out.
    Animated.sequence([
      Animated.timing(opacity, { toValue: 1, duration: 200, useNativeDriver: true }),
      Animated.delay(duration),
      Animated.timing(opacity, { toValue: 0, duration: 200, useNativeDriver: true })
    ]).start();
  }, [message]);

  if (Platform.OS === "android" || !visible) return null;

  return (
    <Animated.View style={[styles.toast, { opacity }]}>
      <Text style={styles.text}>{message}</Text>
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  toast: {
    position: "absolute",
    bottom: 40,
    alignSelf: "center",
    backgroundColor: "rgba(0,0,0,0.8)",
    paddingVertical: 10,
    paddingHorizontal: 20,
    borderRadius: 20
  },
  text: {
    color: "white"
  }
});
```

> 💡 **Note on the fix from the original pattern:** the earlier version of this component called `ToastAndroid.show()` directly inside the function body, meaning it fired as a side effect *during render* — on every re-render, not just when the message actually changed. Side effects belong in `useEffect`, which only runs after render and only when its dependencies (here, `message`) change. This is a general rule, not specific to toasts: any time you're calling an imperative API (network requests, timers, native modules) from inside a component, it belongs in an effect.

**Real-world use case:** Showing "Copied to clipboard!" after a user taps a copy icon — worth acknowledging, not worth blocking them over.

**Other options that exist:**
- **`react-native-toast-message`** — the standard community package for this exact problem, with queuing (multiple toasts stacking), swipe-to-dismiss, and more toast types (success/error/info) out of the box. For production apps, this is generally preferable to a hand-rolled version — the custom component above is worth building once for the learning, but reach for the package afterward.

---

## 4. Activity Modals

Sometimes you want to block interaction entirely while something loads — for example, while waiting on a critical network request the user just triggered. Combining `Modal` with `ActivityIndicator` gives you a blocking loading overlay.

```jsx
// Activity.js
import React from "react";
import { View, Modal, ActivityIndicator } from "react-native";
import styles from "./styles";

export default function Activity({ visible = false, size = "large" }) {
  return (
    <Modal visible={visible} transparent>
      <View style={styles.modalContainer}>
        <ActivityIndicator size={size} />
      </View>
    </Modal>
  );
}
```

Because `transparent` is set, the modal renders as a semi-transparent overlay on top of whatever screen is currently visible — the user sees the app "frozen" behind a dimmed layer with a spinner, rather than being taken to a separate blank screen.

```jsx
// Usage
import React, { useState } from "react";
import { View, Text, Pressable } from "react-native";
import Activity from "./Activity";

export default function FetchStuffScreen() {
  const [loading, setLoading] = useState(false);

  async function fetchStuff() {
    setLoading(true);
    try {
      await new Promise(resolve => setTimeout(resolve, 2000)); // simulate a network call
    } finally {
      setLoading(false);
    }
  }

  return (
    <View>
      <Pressable onPress={fetchStuff}>
        <Text>Fetch Stuff...</Text>
      </Pressable>
      <Activity visible={loading} />
    </View>
  );
}
```

The modal is shown for exactly as long as `loading` is `true` — set right before the async call starts, and cleared in a `finally` block so it's hidden whether the call succeeds or fails.

**Real-world use case:** Blocking the screen with a spinner while processing a payment — the user genuinely shouldn't be able to interact with anything else until that specific operation resolves.

**Other options that exist:**
- **A non-blocking inline spinner** (from Chapter 18) — worth reconsidering whether you need a *blocking* modal at all. If the user can safely keep using other parts of the screen while something loads in the background, an inline `ActivityIndicator` is less disruptive than a full-screen block.
- **`react-native-modal` with a loading preset** — if you're already using that package for other modals, it can standardize the loading overlay's animation and styling alongside your other modal types.

---

## 5. Exercises

1. **Alert.alert():** Build a "Sign Out" button that shows a confirmation dialog using `Alert.alert()` before actually signing the user out.
2. **Custom Modal:** Build a "Rate this app" confirmation modal with three buttons — "Rate Now," "Remind Me Later," and "No Thanks" — each calling a different handler.
3. **Toast:** Extend the `Toast` component to accept a `type` prop (`"success"`, `"error"`, `"info"`) that changes the background color accordingly.
4. **Activity modal:** Wire up the `Activity` component to a real `fetch()` call (e.g., to a public test API), showing the modal while the request is in flight and hiding it once data arrives or an error occurs.
5. **Challenge:** Build a form submission flow that combines all four patterns: an `Activity` modal while submitting, an `ErrorModal` if the submission fails, a `ConfirmationModal` if it succeeds, and a `Toast` confirming when the user dismisses the success modal.

---

## 6. Summary

- **Alerts** need the user's attention and may require acknowledgment; **notifications** are passive and self-dismissing; **confirmations** are alerts that specifically ask the user to confirm or acknowledge before continuing.
- For simple confirmations, `Alert.alert()` is the fastest built-in option — no custom styling needed, and it matches the platform's native look automatically.
- For fully custom-styled confirmations, build your own component around `Modal`, combining style objects as arrays to layer variant-specific overrides (e.g., error styles) on top of shared base styles.
- Passive notifications should disappear on their own — on Android, `ToastAndroid` is the platform convention; for a cross-platform solution, either build a small custom animated toast or use `react-native-toast-message` for production use. Any imperative API call like this belongs inside `useEffect`, not directly in the render body.
- `Modal` + `ActivityIndicator` gives you a blocking loading overlay — appropriate when the user genuinely shouldn't interact with anything else until an operation completes, as opposed to a non-blocking inline spinner.
