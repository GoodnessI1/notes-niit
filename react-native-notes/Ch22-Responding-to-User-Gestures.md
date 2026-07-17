# Chapter 22: Responding to User Gestures in React Native

Every example so far in this course has relied on user gestures, but on the web, you're mostly dealing with mouse events — clicks, hovers, drags with a pointer. Touchscreens are fundamentally different: users manipulate elements directly with their fingers. This chapter covers three gesture-driven patterns: scrolling, touch feedback, and swipe-to-dismiss.

---

## 1. Scrolling With Your Fingers — `<ScrollView>`

On the web, scrolling happens via a mouse-dragged scrollbar or a mouse wheel. On mobile, there's no mouse — scrolling is a finger gesture, dragging content up or down directly. `<ScrollView>` handles all of this gesture complexity for you. You've actually already used it indirectly — `FlatList` (covered in the list-rendering chapter) has a `ScrollView` built into it.

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `horizontal` | Scrolls left-right instead of up-down |
| `showsVerticalScrollIndicator` / `showsHorizontalScrollIndicator` | Set to `false` to hide the scrollbar |
| `pagingEnabled` | Snaps scrolling to full-width/height "pages" instead of free scrolling |
| `scrollEventThrottle` | How often (in ms) `onScroll` fires while scrolling — lower means more frequent updates |
| `onScroll` | Fires repeatedly during scrolling, giving you position data via `event.nativeEvent.contentOffset` |
| `contentContainerStyle` | Styles applied to the *content* inside the scroll view, not the scroll view itself (useful for padding, centering) |

### Example

```jsx
import React from "react";
import { Text, ScrollView, ActivityIndicator, Switch, View } from "react-native";

export default function ScrollingWithFingers() {
  return (
    <View style={{ flex: 1 }}>
      <ScrollView style={{ flex: 1 }}>
        {new Array(6).fill(null).map((v, i) => (
          <View key={i}>
            <Text>Some text</Text>
            <ActivityIndicator size="large" />
            <Switch />
          </View>
        ))}
      </ScrollView>
    </View>
  );
}
```

> 💡 **Note:** `ScrollView` isn't useful on its own — it exists to wrap other components, and it needs a defined height to size correctly. Giving it `flex: 1` (so it fills its parent's available space) is the standard, straightforward way to do this. You may occasionally see older code using a workaround like `height: 1` combined with `alignSelf: "stretch"` — that was a fix for a flexbox layout quirk in much older React Native versions and is no longer necessary; `flex: 1` is the current correct approach.

**Real-world use case:** A settings screen with more content than fits on one screen — wrapping the whole thing in a `ScrollView` lets the user scroll through sections instead of everything being cut off.

**Other options that exist:**
- **`FlatList`** — for long, uniform lists of data (covered earlier in this course), since it only renders items currently on screen rather than all of them at once, which `ScrollView` does not do.
- **`SectionList`** — like `FlatList`, but for data organized into labeled sections/groups.

---

## 2. Giving Touch Feedback — `Pressable`

Plain text or a plain `View` gives the user no visual cue that it's tappable. On the web, you'd style a link; on mobile, there's no such thing as a "link" — you need a component that visually responds to being pressed.

React Native's older approach used separate components — `TouchableOpacity` (dims the element on press) and `TouchableHighlight` (overlays a highlight color on press). Both still work, but the React Native team now recommends **`Pressable`** for new code: a single, more flexible component that can express either effect (and more) through one consistent API.

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `onPress` | Fires when the press completes |
| `onPressIn` / `onPressOut` | Fire when the touch begins/ends, before `onPress` resolves — useful for custom press animations |
| `disabled` | Prevents interaction when `true` |
| `style` (as a function) | Can receive `({ pressed }) => ({...})`, letting you change styles conditionally based on press state |
| `android_ripple` | Android-only — shows a native ripple effect on press |
| `hitSlop` | Expands the touchable area beyond the element's visible bounds, without changing its visual size |

### Example: A Button Supporting Both Feedback Styles

```jsx
// Button.js
import React from "react";
import { Text, Pressable, StyleSheet } from "react-native";

export default function Button({ label, onPress, feedback = "opacity" }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [
        styles.button,
        pressed && feedback === "opacity" && styles.pressedOpacity,
        pressed && feedback === "highlight" && styles.pressedHighlight
      ]}
    >
      <Text style={styles.buttonText}>{label}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  button: {
    padding: 10,
    margin: 5,
    backgroundColor: "azure",
    borderWidth: 1,
    borderRadius: 4,
    borderColor: "slategrey"
  },
  pressedOpacity: {
    opacity: 0.5
  },
  pressedHighlight: {
    backgroundColor: "rgba(112,128,144,0.3)"
  },
  buttonText: {
    color: "slategrey"
  }
});
```

```jsx
// Usage
import React from "react";
import { View } from "react-native";
import Button from "./Button";

export default function App() {
  return (
    <View>
      <Button onPress={() => {}} label="Opacity" feedback="opacity" />
      <Button onPress={() => {}} label="Highlight" feedback="highlight" />
    </View>
  );
}
```

The `style` prop receives a function here instead of a plain object — React Native calls it with `{ pressed }`, letting you conditionally apply different styles depending on whether the element is currently being touched. This single component replaces what previously required switching between two entirely different components.

**Real-world use case:** A list of selectable cards where tapping one should visibly "press down" (dim or highlight) to confirm the tap registered, before navigating to a detail screen.

**Other options that exist:**
- **`TouchableOpacity`/`TouchableHighlight`** — still fully functional and appear often in existing codebases and older tutorials, but `Pressable` is the currently recommended approach for new code.
- **`react-native-paper`'s `Button`** — a ready-made, Material Design themed button if you don't need custom press-feedback logic.

---

## 3. Swipeable and Cancellable

Native mobile apps often feel more discoverable than mobile web apps because gestures are reversible: a user can press down, start dragging, and if they're unsure what will happen, simply lift their finger and watch the element snap back — no commitment required until the gesture completes.

The React Native community standard for this exact pattern — swipe-to-reveal-actions or swipe-to-dismiss — is the `Swipeable` component from **`react-native-gesture-handler`**, which is what most production apps reach for rather than hand-rolling the gesture logic.

```bash
npx expo install react-native-gesture-handler
```

Your app's root component needs to be wrapped once in `GestureHandlerRootView` for gestures to work:

```jsx
// App.js
import React from "react";
import { GestureHandlerRootView } from "react-native-gesture-handler";
import SwipeableList from "./SwipeableList";

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <SwipeableList />
    </GestureHandlerRootView>
  );
}
```

### Building a Swipe-to-Delete List

```jsx
// SwipeableItem.js
import React from "react";
import { Text, View, Pressable } from "react-native";
import { Swipeable } from "react-native-gesture-handler";

function RightAction({ onPress }) {
  return (
    <Pressable onPress={onPress} style={{ backgroundColor: "firebrick", justifyContent: "center", paddingHorizontal: 20 }}>
      <Text style={{ color: "white" }}>Delete</Text>
    </Pressable>
  );
}

export default function SwipeableItem({ name, onSwipeDelete }) {
  return (
    <Swipeable renderRightActions={() => <RightAction onPress={onSwipeDelete} />}>
      <View style={{ padding: 15, backgroundColor: "azure", borderWidth: 1, borderColor: "slategrey" }}>
        <Text style={{ color: "slategrey" }}>{name}</Text>
      </View>
    </Swipeable>
  );
}
```

```jsx
// SwipeableList.js
import React, { useState } from "react";
import { View } from "react-native";
import SwipeableItem from "./SwipeableItem";

export default function SwipeableList() {
  const [items, setItems] = useState(
    new Array(8).fill(null).map((v, id) => ({ id, name: "Swipe Me" }))
  );

  function onSwipeDelete(id) {
    setItems(current => current.filter(item => item.id !== id));
  }

  return (
    <View>
      {items.map(item => (
        <SwipeableItem key={item.id} name={item.name} onSwipeDelete={() => onSwipeDelete(item.id)} />
      ))}
    </View>
  );
}
```

`renderRightActions` renders whatever you want revealed when the user swipes an item to the left — here, a red "Delete" button. The library handles all the drag tracking, snapping, and cancel-on-release behavior for you; you only need to supply what the revealed action looks like and what happens when it's pressed.

> 📝 **Why not build this with a horizontal `ScrollView`?** An earlier, common teaching pattern hand-built swipe-to-delete using a horizontal, paginated `ScrollView`, checking `contentOffset.x` against a fixed pixel threshold to detect a completed swipe. That approach has two real weaknesses worth knowing about even if you don't use it: comparing a scroll offset with exact equality (`=== 200`) is unreliable, since floating-point scroll positions rarely land on an exact integer — a `>=` threshold comparison is safer; and hardcoding pixel widths ties your gesture logic to a specific screen size. `react-native-gesture-handler`'s `Swipeable` avoids both problems entirely, which is a big part of why it's the standard choice today.

**Real-world use case:** An email or task list where swiping an item left reveals a "Delete" or "Archive" action — one of the most common mobile interaction patterns.

**Other options that exist:**
- **`renderLeftActions`** (alongside `renderRightActions`) — reveals actions when swiping in the opposite direction, useful for something like "Mark as read" on a right-swipe and "Delete" on a left-swipe.
- **A hand-built `ScrollView`-based version** — still a legitimate learning exercise for understanding gesture-driven UI from first principles, even though it's not what you'd ship in production.

---

## 4. Exercises

1. **ScrollView:** Build a scrollable list of 20 numbered cards, and add a "Scroll to Top" button that uses a `ref` and `scrollTo({ y: 0 })` to jump back to the beginning.
2. **Pressable:** Extend the `Button` component with a third `feedback` option, `"scale"`, that shrinks the button slightly (`transform: [{ scale: 0.95 }]`) while pressed.
3. **Swipeable:** Add a `renderLeftActions` action to the swipeable list that marks an item as "Done" (e.g., strikes through its text) instead of deleting it.
4. **Challenge:** Combine all three — build a scrollable to-do list where each item uses `Pressable` to toggle completion on tap, and `Swipeable` to reveal a delete action on swipe.

---

## 5. Summary

- **`ScrollView`** handles the complexity of finger-driven scrolling for you — give it `flex: 1` so it can size correctly within its parent.
- **`Pressable`** is the current recommended way to give elements touch feedback, replacing the older `TouchableOpacity`/`TouchableHighlight` split with a single component whose `style` can respond to press state directly.
- **Swipe gestures** are best implemented with `react-native-gesture-handler`'s `Swipeable` component — the community-standard tool for swipe-to-reveal and swipe-to-dismiss patterns, avoiding the fragility of hand-rolled scroll-offset detection.
