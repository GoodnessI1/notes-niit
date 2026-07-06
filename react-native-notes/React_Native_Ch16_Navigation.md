# Chapter 16 — Navigation with React Navigation

> **For the teacher:** Teaching notes are marked with `📌 TEACHER NOTE`.
> Code blocks are ready to live-code. Old API is shown with ❌, new API with ✅.
> Exercises are at the end.
>
> **For the student (after class):** Read through section by section.
> Every concept has an explanation, an analogy, and working code.
> Try the exercises on your own.

---

## Table of Contents

- [Why Navigation?](#why)
- [Installing React Navigation](#installing)
- [Old API vs New API — Know the Difference](#old-vs-new)
- [Part 1 — Navigation Basics (Stack)](#part-1)
- [Part 2 — Route Parameters](#part-2)
- [Part 3 — The Navigation Header](#part-3)
- [Part 4 — Tab and Drawer Navigation](#part-4)
- [Part 5 — Sharing State Across Screens](#part-5)
- [Quick Reference](#reference)
- [Exercises](#exercises)

---

<a name="why"></a>
## Why Navigation?

A real mobile app has multiple screens. You need a way to:
- Move from one screen to another
- Pass data between screens
- Go back to a previous screen
- Show tab bars or side drawer menus

React Native does not have a built-in navigation system that the community
agreed on. Instead the community built **React Navigation**, which has become
the standard library everyone uses.

---

<a name="installing"></a>
## Installing React Navigation

### Core (always required)

```bash
npm install @react-navigation/native

# Peer dependencies via Expo
npx expo install react-native-screens react-native-safe-area-context
```

### Stack Navigation (screen to screen)

```bash
npm install @react-navigation/native-stack
```

### Tab Navigation

```bash
npm install @react-navigation/bottom-tabs
```

### Drawer Navigation

```bash
npm install @react-navigation/drawer
npx expo install react-native-gesture-handler react-native-reanimated
```

> 📌 **TEACHER NOTE:** Install only what you need for that class session.
> Start with just the core + native-stack. Add tabs and drawer when you reach
> those sections. Installing everything upfront overwhelms students with
> unfamiliar package names.

---

<a name="old-vs-new"></a>
## Old API vs New API — Know the Difference

> 📌 **TEACHER NOTE:** Teach this table before showing any code. Students WILL
> find old tutorials online — from 2020, 2021 — and they will be confused when
> the code doesn't match what you're teaching. This table gives them context
> to recognise outdated code and know to look for a newer source.

The slides use **React Navigation v4** (2019). The current version is **v6/v7**
and the API changed completely.

| Old (v4) — ❌ DEPRECATED | New (v6+) — ✅ USE THIS |
|---|---|
| `import { createAppContainer } from 'react-navigation'` | **Removed entirely** |
| `import { createStackNavigator } from 'react-navigation-stack'` | `import { createNativeStackNavigator } from '@react-navigation/native-stack'` |
| `createAppContainer(createStackNavigator({...}))` | `<NavigationContainer><Stack.Navigator>` |
| `navigation.getParam('title')` | `route.params.title` |
| `Component.navigationOptions = { title: 'Home' }` | `options` prop on `<Stack.Screen>` |
| `screenProps` for passing state | React Context |
| `createBottomTabNavigator` from `react-navigation-tabs` | from `@react-navigation/bottom-tabs` |
| `createDrawerNavigator` from `react-navigation-drawer` | from `@react-navigation/drawer` |

The **concept** is the same. The syntax is what changed entirely.

---

<a name="part-1"></a>
## Part 1 — Navigation Basics (Stack)

### How Stack Navigation Works

React Navigation's stack navigator works like a **stack of cards**:

```
Start:                Navigate to Details:       Go Back:
┌──────────┐          ┌──────────────────┐       ┌──────────┐
│   Home   │          │     Details      │       │   Home   │
│  Screen  │    ──►   ├──────────────────┤  ──►  │  Screen  │
└──────────┘          │   Home Screen    │       └──────────┘
                      └──────────────────┘
                      (Details on top of Home)
```

- `navigation.navigate('Details')` pushes Details onto the stack
- `navigation.goBack()` pops the top screen, returning to the previous one
- The back button in the header does the same as `goBack()`

---

### ❌ Old API (v4) — For Reference Only

```jsx
// ❌ OLD App.js (v4) — DO NOT USE IN NEW PROJECTS
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
```

```jsx
// ❌ OLD Home.js (v4)
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

### ✅ New API (v6) — Use This

**`App.js`:**

```jsx
// ✅ NEW App.js (v6)
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

**`screens/HomeScreen.js`:**

```jsx
// ✅ NEW HomeScreen.js (v6)
import { View, Text, Button, StyleSheet } from 'react-native';

export default function HomeScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Home Screen</Text>
      <Button
        title="Go to Settings"
        onPress={() => navigation.navigate('Settings')}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: 'center', justifyContent: 'center' },
  title: { fontSize: 24, marginBottom: 20 },
});
```

**`screens/SettingsScreen.js`:**

```jsx
// ✅ NEW SettingsScreen.js (v6)
import { View, Text, Button, StyleSheet } from 'react-native';

export default function SettingsScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Settings Screen</Text>
      <Button
        title="Go Back"
        onPress={() => navigation.goBack()}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: 'center', justifyContent: 'center' },
  title: { fontSize: 24, marginBottom: 20 },
});
```

> 📌 **TEACHER NOTE:** Walk through the v6 structure carefully:
>
> - `NavigationContainer` — wraps the entire app. Think of it as the
>   navigation root. Everything goes inside it.
> - `Stack.Navigator` — defines the navigation type and which screen to show first
> - `Stack.Screen` — registers each screen. Maps a `name` to a `component`
> - The `navigation` prop — automatically injected into every registered screen.
>   Students don't pass it manually — React Navigation does it.
>
> Ask students: "Where does the `navigation` prop come from in HomeScreen?"
> It comes from React Navigation automatically, because `HomeScreen` is
> registered as a `<Stack.Screen>`.

---

<a name="part-2"></a>
## Part 2 — Route Parameters

### The Concept

When navigating to a screen, you often need to pass data along.
For example, tapping a product in a list should open a details screen
showing that specific product's information.

In web React Router you used URL params (`:id` in the route path).
In React Navigation you pass data as the second argument to `navigate()`.

---

### ❌ Old API (v4) — Passing and Receiving Params

```jsx
// ❌ OLD — passing params (v4)
navigation.navigate('Details', { title: 'First Item' });

// ❌ OLD — receiving params (v4) — getParam is removed in v5+
export default function Details({ navigation }) {
  const title = navigation.getParam('title'); // ← no longer exists
}
```

---

### ✅ New API (v6) — Passing and Receiving Params

```jsx
// ✅ NEW — passing params
navigation.navigate('Details', {
  id: 'item-001',
  title: 'First Item',
  price: 2500,
  stock: 10,
});
```

```jsx
// ✅ NEW — receiving params via the `route` prop (NOT navigation)
export default function DetailsScreen({ route, navigation }) {
  const { id, title, price, stock } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{title}</Text>
      <Text>Price: ₦{price}</Text>
      <Text>In Stock: {stock}</Text>
    </View>
  );
}
```

> 📌 **TEACHER NOTE:** The biggest change from v4 is this:
> - v4: params came from `navigation.getParam('key')`
> - v6: params come from `route.params.key`
>
> In v6, every screen gets TWO props injected automatically:
> - `navigation` — for navigating (navigate, goBack, etc.)
> - `route` — for reading params and current route info
>
> Students often try to get params from `navigation` out of habit from
> old tutorials. When it doesn't work, check that they're using `route.params`.

---

### Full Example — Home with Multiple Items

```jsx
// ✅ screens/HomeScreen.js — list of items, each navigates to Details
import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';

const items = [
  { id: 'first',  title: 'First Item',  price: 2500, stock: 10 },
  { id: 'second', title: 'Second Item', price: 5000, stock: 0  },
  { id: 'third',  title: 'Third Item',  price: 1200, stock: 200 },
];

export default function HomeScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.heading}>Home Screen</Text>
      {items.map(item => (
        <TouchableOpacity
          key={item.id}
          style={styles.itemButton}
          onPress={() => navigation.navigate('Details', item)}
        >
          <Text style={styles.itemText}>{item.title}</Text>
        </TouchableOpacity>
      ))}
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: 'center', justifyContent: 'center' },
  heading: { fontSize: 22, fontWeight: 'bold', marginBottom: 20 },
  itemButton: {
    backgroundColor: '#007AFF',
    padding: 12,
    borderRadius: 8,
    width: 200,
    alignItems: 'center',
    marginBottom: 10,
  },
  itemText: { color: 'white', fontSize: 16 },
});
```

---

<a name="part-3"></a>
## Part 3 — The Navigation Header

Every screen in a Stack navigator automatically gets a header bar at the top.
By default it's plain. You configure it to show the right title, buttons, and
style for each screen.

---

### ❌ Old API (v4) — Static and Dynamic Header Options

```jsx
// ❌ OLD (v4) — static title
Home.navigationOptions = {
  title: 'Home',
};

// ❌ OLD (v4) — dynamic title using navigation.getParam
Details.navigationOptions = ({ navigation }) => ({
  title: navigation.getParam('title'),
  headerRight: () => (
    <Button
      title="Buy"
      disabled={navigation.getParam('stock') === 0}
      onPress={() => {}}
    />
  ),
});
```

---

### ✅ New API (v6) — Options Prop on Stack.Screen

**Static title:**

```jsx
// ✅ NEW — set directly on Stack.Screen in App.js
<Stack.Screen
  name="Home"
  component={HomeScreen}
  options={{ title: 'Home' }}
/>
```

**Dynamic title and header button from route params:**

```jsx
// ✅ NEW — options receives { route, navigation }
<Stack.Screen
  name="Details"
  component={DetailsScreen}
  options={({ route }) => ({
    title: route.params.title,
    headerRight: () => (
      <Button
        title="Buy"
        disabled={route.params.stock === 0}
        onPress={() => alert('Bought!')}
      />
    ),
  })}
/>
```

**Setting header from inside the screen component:**

```jsx
// ✅ NEW — using useLayoutEffect inside the screen
import { useLayoutEffect } from 'react';
import { View, Text, Button } from 'react-native';

export default function DetailsScreen({ route, navigation }) {
  const { title, stock, content } = route.params;

  useLayoutEffect(() => {
    navigation.setOptions({
      title: title,
      headerRight: () => (
        <Button
          title="Buy"
          disabled={stock === 0}
          onPress={() => alert('Bought!')}
        />
      ),
    });
  }, [navigation, title, stock]);

  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text style={{ fontSize: 18 }}>{content}</Text>
    </View>
  );
}
```

> 📌 **TEACHER NOTE:** There are two valid v6 approaches shown above:
>
> 1. Define `options` on `<Stack.Screen>` in App.js — good when the options
>    are straightforward and don't need internal component logic
> 2. Use `navigation.setOptions()` inside the screen with `useLayoutEffect` —
>    good when the header needs to interact with internal component state
>
> For beginners, approach 1 is simpler. Introduce approach 2 when students
> need the header to change based on component-level state.

---

<a name="part-4"></a>
## Part 4 — Tab and Drawer Navigation

Stack navigation is screen-to-screen (push/pop). Tab and Drawer navigation
give users a persistent way to switch between the main sections of your app.

```
Stack:     Home → Details → (back) → Home
           (linear, like a history stack)

Tabs:      [Home] [News] [Settings]
           (always visible at bottom, tap to switch)

Drawer:    ≡ → [Home, News, Settings]
           (slide in from the side)
```

---

### ❌ Old API (v4) — Tab and Drawer

```jsx
// ❌ OLD (v4) — platform-specific navigator selection
import { createBottomTabNavigator } from 'react-navigation-tabs';
import { createDrawerNavigator } from 'react-navigation-drawer';

const { createNavigator } = Platform.select({
  ios: { createNavigator: createBottomTabNavigator },
  android: { createNavigator: createDrawerNavigator },
});

export default createAppContainer(
  createNavigator({ Home, News, Settings }, { initialRouteName: 'Home' })
);
```

---

### ✅ New API (v6) — Bottom Tab Navigator

```jsx
// ✅ NEW — Tab Navigation (v6)
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

---

### ✅ New API (v6) — Drawer Navigator

```jsx
// ✅ NEW — Drawer Navigation (v6)
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

---

### ✅ Platform-Specific Navigator (Tabs on iOS, Drawer on Android)

```jsx
// ✅ NEW — platform-aware navigation (v6)
import { Platform } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createDrawerNavigator } from '@react-navigation/drawer';
import HomeScreen from './screens/HomeScreen';
import NewsScreen from './screens/NewsScreen';
import SettingsScreen from './screens/SettingsScreen';

const Tab = createBottomTabNavigator();
const Drawer = createDrawerNavigator();

// Pick the right navigator based on the platform
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

> 📌 **TEACHER NOTE:** Note the pattern — `Platform.OS === 'ios'` returns
> either the Tab or Drawer navigator, stored in `Navigator`. Then
> `<Navigator.Navigator>` and `<Navigator.Screen>` work the same regardless
> of which one was selected. The API shape of Tab and Drawer is identical —
> only what gets rendered changes.

---

<a name="part-5"></a>
## Part 5 — Sharing State Across Screens

### The Problem

App-level state (a shopping cart, user authentication, product stock) needs to
be accessible from multiple screens. But the navigator sits between `App` and
your screens, making it tricky to pass state down.

---

### ❌ Old API (v4) — `screenProps`

```jsx
// ❌ OLD (v4) — passing state via screenProps (removed in v5)
return <Nav screenProps={{ stock, updateStock }} />;

// In the screen:
export default function DetailsScreen({ screenProps }) {
  const { stock, updateStock } = screenProps; // ← no longer exists
}
```

`screenProps` was removed in React Navigation v5. It does not exist in v6.

---

### ✅ New API (v6) — React Context

The modern solution is **React Context** — the same pattern you already know
from web React. Context doesn't care about the navigation layer. Any component
anywhere in the tree can consume it.

**Step 1 — Create the Context:**

```jsx
// context/StockContext.js
import { createContext, useContext, useState } from 'react';

const StockContext = createContext();

export function StockProvider({ children }) {
  const [stock, setStock] = useState({
    first: 10,
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

// Custom hook — makes consuming the context cleaner
export function useStock() {
  return useContext(StockContext);
}
```

**Step 2 — Wrap the App:**

```jsx
// App.js — StockProvider wraps NavigationContainer
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { StockProvider } from './context/StockContext';
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

**Step 3 — Consume in Any Screen:**

```jsx
// screens/DetailsScreen.js
import { View, Text, Button, StyleSheet } from 'react-native';
import { useStock } from '../context/StockContext';

export default function DetailsScreen({ route }) {
  const { title, content, id } = route.params;
  const { stock, updateStock } = useStock();

  const currentStock = stock[id];

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{title}</Text>
      <Text style={styles.content}>{content}</Text>
      <Text style={styles.stock}>
        In Stock: {currentStock === 0 ? 'Out of stock' : currentStock}
      </Text>
      <Button
        title="Buy"
        onPress={() => updateStock(id)}
        disabled={currentStock === 0}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: 'center', justifyContent: 'center', padding: 20 },
  title: { fontSize: 24, fontWeight: 'bold', marginBottom: 10 },
  content: { fontSize: 16, color: '#555', marginBottom: 20, textAlign: 'center' },
  stock: { fontSize: 18, marginBottom: 20 },
});
```

> 📌 **TEACHER NOTE:** This is the moment to connect back to web React.
> "We already know Context. We learned it when we were building our web React
> apps. We're using the exact same pattern here. The only difference is that
> the components consuming the context happen to be screens in a mobile app
> rather than components in a web app."
>
> This is a confidence builder — they didn't learn something new, they applied
> something they already know to a new context (no pun intended).

---

<a name="reference"></a>
## Quick Reference

### The Three Navigator Types

| Navigator | Package | Use Case |
|---|---|---|
| Stack | `@react-navigation/native-stack` | Screen-to-screen (most common) |
| Bottom Tabs | `@react-navigation/bottom-tabs` | Tab bar at bottom of screen |
| Drawer | `@react-navigation/drawer` | Slide-in side menu |

### Key Navigation Methods

| Method | What It Does |
|---|---|
| `navigation.navigate('Screen')` | Go to a screen |
| `navigation.navigate('Screen', { key: value })` | Go to a screen with params |
| `navigation.goBack()` | Return to previous screen |
| `navigation.push('Screen')` | Push a screen even if already in stack |
| `navigation.replace('Screen')` | Replace current screen (no back button) |
| `navigation.popToTop()` | Jump back to the very first screen |

### Accessing Route Params

```jsx
// Always from route.params — NOT from navigation
export default function MyScreen({ route, navigation }) {
  const { id, title, price } = route.params;
}
```

### Setting Header Options

```jsx
// On Stack.Screen in App.js (static or param-based)
<Stack.Screen
  name="Details"
  component={DetailsScreen}
  options={({ route }) => ({
    title: route.params.title,
    headerRight: () => <Button title="Save" onPress={() => {}} />,
  })}
/>

// Or from inside the screen (for state-based changes)
useLayoutEffect(() => {
  navigation.setOptions({ title: 'Dynamic Title' });
}, [navigation]);
```

### v6 App Structure

```jsx
<StockProvider>                          ← Context (optional, for shared state)
  <NavigationContainer>                  ← Navigation root (always required)
    <Stack.Navigator initialRouteName="Home">
      <Stack.Screen name="Home" component={HomeScreen} options={{ title: 'Home' }} />
      <Stack.Screen name="Details" component={DetailsScreen} options={...} />
    </Stack.Navigator>
  </NavigationContainer>
</StockProvider>
```

---

<a name="exercises"></a>
## Chapter 16 Exercises

### Exercise 1 — Two-Screen App
Create a two-screen stack navigation app:
- **Home screen** with a button "Go to About"
- **About screen** with some text about a fictional app
- A working back button that returns to Home

### Exercise 2 — Passing Parameters
Extend Exercise 1:
- On the Home screen, show a list of 3 team members (as buttons)
- When a member is tapped, navigate to a Profile screen
- The Profile screen displays the team member's name passed as a param

### Exercise 3 — Dynamic Header
Extend Exercise 2:
- Set the navigation header title dynamically to the team member's name
- Add a "Message" button in the header right area
- The button should show an alert when pressed

### Exercise 4 — Tab Navigation
Build a new app with three tabs using `createBottomTabNavigator`:
- Home tab
- Search tab  
- Profile tab

Each tab should display a simple screen with just a title text.

### Exercise 5 — Shared State with Context (Advanced)
Build a mini shopping app:
- Home screen with a list of 5 products (name and price)
- Tapping a product navigates to a Details screen
- Details screen has an "Add to Cart" button
- A cart count is visible somewhere on the Home screen
- When you add an item and go back, the cart count on Home updates

Use React Context to share the cart state between screens.

---

*Chapter 16 — Navigation with React Navigation.*
*Updated to React Navigation v6. Old v4 API shown for reference only.*
*`screenProps` removed — use React Context instead.*
*`navigation.getParam()` removed — use `route.params` instead.*
