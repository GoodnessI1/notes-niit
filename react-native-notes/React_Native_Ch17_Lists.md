# Chapter 17 — Lists in React Native
### FlatList, Sorting, Filtering, Fetching & Lazy Loading

> **For the teacher:** Teaching notes are marked with `📌 TEACHER NOTE`.
> Code blocks are ready to live-code. Exercises are at the end.
>
> **For the student (after class):** Read through section by section.
> Every concept has an explanation, an analogy, and working code.
> Try the exercises on your own.

---

## Table of Contents

- [Why Lists Matter](#why-lists)
- [Part 1 — Rendering Data Collections with FlatList](#part-1)
- [Part 2 — Sorting and Filtering Lists](#part-2)
- [Part 3 — Fetching List Data from an API](#part-3)
- [Part 4 — Lazy List Loading (Infinite Scroll)](#part-4)
- [Exercises](#exercises)
- [Quick Reference](#quick-reference)

---

<a name="why-lists"></a>
## Why Lists Matter

Almost every real-world app you can think of is built around lists:

```
WhatsApp    → list of chats
Instagram   → list of posts (infinite scroll)
Jumia       → list of products (with filtering and sorting)
Twitter/X   → list of tweets (infinite scroll)
Contacts    → list of people (with search/filter)
```

Knowing how to render, sort, filter, and lazily load lists is one of the most
practical skills in React Native. This chapter covers all of it.

---

<a name="part-1"></a>
## Part 1 — Rendering Data Collections with FlatList

### The Web React Way vs The React Native Way

In web React you rendered a list like this:

```jsx
// Web React
<ul>
  {items.map(item => (
    <li key={item.id}>{item.name}</li>
  ))}
</ul>
```

In React Native there is no `<ul>` or `<li>`. Instead React Native gives you
a dedicated component called **`FlatList`**.

```jsx
// React Native
<FlatList
  data={items}
  renderItem={({ item }) => <Text>{item.name}</Text>}
/>
```

> 📌 **TEACHER NOTE:** Ask students "Why not just use `.map()` in React Native
> like we did on the web?" The answer is performance. `.map()` renders ALL items
> at once even if there are 1000 of them. FlatList is smart — it only renders
> what is currently visible on screen and recycles components as you scroll.
> This is called **virtualisation** and it's the core reason FlatList exists.

---

### How FlatList Works

```
┌─────────────────────────────────────┐
│           Phone Screen              │
│  ┌───────────────────────────────┐  │
│  │  Item 1   ← rendered          │  │
│  │  Item 2   ← rendered          │  │
│  │  Item 3   ← rendered          │  │
│  │  Item 4   ← rendered          │  │
│  └───────────────────────────────┘  │
│                                     │
│  Item 5  ← NOT rendered yet         │
│  Item 6  ← NOT rendered yet         │
│  Item 7  ← NOT rendered yet         │
│  ...                                │
└─────────────────────────────────────┘

As you scroll DOWN:
- Item 1 leaves screen → recycled
- Item 5 enters screen → rendered
- Item 6 enters screen → rendered
```

FlatList manages all of this automatically. You just give it data and tell it
how to render one item.

---

### The Two Required Props

`FlatList` has two props you must always provide:

| Prop | Type | What It Does |
|---|---|---|
| `data` | Array | The array of items to render |
| `renderItem` | Function | How to render each single item |

```jsx
<FlatList
  data={myArray}           // your data source
  renderItem={({ item }) => ...}  // how to display one item
/>
```

The `renderItem` function receives an object. You destructure `item` from it —
`item` is the current element from your `data` array.

---

### Your First FlatList — 100 Item List

Let's build a list of 100 items from scratch.

**Step 1 — Create the data**

```jsx
// Generate an array of 100 objects
// Each object has a key (required) and a value (what we display)
const data = new Array(100)
  .fill(null)
  .map((_, i) => ({
    key: i.toString(),   // FlatList needs a unique key for each item
    value: `Item ${i}`,  // The display text
  }));

// What this produces:
// [
//   { key: '0', value: 'Item 0' },
//   { key: '1', value: 'Item 1' },
//   { key: '2', value: 'Item 2' },
//   ...
// ]
```

> 📌 **TEACHER NOTE:** The `key` field on each object is important. FlatList
> uses it the same way React uses the `key` prop on mapped elements — to track
> which items changed. Without unique keys, React can't efficiently update the list.
> This is the same concept from web React, just written differently.

**Step 2 — The full App.js**

```jsx
import { Text, View, FlatList, StyleSheet, Platform, StatusBar } from 'react-native';

const data = new Array(100)
  .fill(null)
  .map((_, i) => ({
    key: i.toString(),
    value: `Item ${i}`,
  }));

export default function App() {
  return (
    <View style={styles.container}>
      <FlatList
        data={data}
        renderItem={({ item }) => (
          <Text style={styles.item}>{item.value}</Text>
        )}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    ...Platform.select({
      ios: { paddingTop: 20 },
      android: { paddingTop: StatusBar.currentHeight },
    }),
  },
  item: {
    margin: 5,
    padding: 10,
    color: 'slategrey',
    backgroundColor: 'ghostwhite',
    textAlign: 'center',
    fontSize: 16,
  },
});
```

> 📌 **TEACHER NOTE:** Point out two things here:
>
> 1. The `container` needs `flex: 1` and `flexDirection: 'column'` — this gives
>    the list height. Without height, FlatList cannot scroll. This is the number
>    one mistake beginners make with FlatList.
>
> 2. FlatList must be inside a View that has a defined height, otherwise it
>    has nothing to scroll within.

---

### Why the Container Needs Height

```
WITHOUT flex: 1 on container:
┌──────────────────────────┐
│  container height = 0    │  ← FlatList has no space to scroll in
│  [nothing visible]       │
└──────────────────────────┘

WITH flex: 1 on container:
┌──────────────────────────┐
│  container fills screen  │
│  Item 0                  │
│  Item 1                  │
│  Item 2                  │
│  Item 3                  │
│  Item 4                  │
│  [scroll for more...]    │
└──────────────────────────┘
```

---

<a name="part-2"></a>
## Part 2 — Sorting and Filtering Lists

### The Plan

A bare list of items is useful, but real apps let users **search** and **sort**.
We're going to add that to our list, but first let's talk about how to structure
the components properly.

### Component Architecture

Rather than cramming everything into one component, we split responsibilities:

```
┌─────────────────────────────────────────────┐
│              ListContainer                  │
│   (owns all state: data, filter, sort)      │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │                List                   │  │
│  │  ┌─────────────────────────────────┐  │  │
│  │  │          ListControls           │  │  │
│  │  │  ┌──────────────┬────────────┐  │  │  │
│  │  │  │  ListFilter  │  ListSort  │  │  │  │
│  │  │  └──────────────┴────────────┘  │  │  │
│  │  └─────────────────────────────────┘  │  │
│  │                                       │  │
│  │  ┌─────────────────────────────────┐  │  │
│  │  │            FlatList             │  │  │
│  │  │  (renders the actual items)     │  │  │
│  │  └─────────────────────────────────┘  │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
```

Each component has ONE job:

| Component | Job |
|---|---|
| `ListContainer` | Owns all state, fetches data, passes things down |
| `List` | Receives data and renders the controls + FlatList |
| `ListControls` | Holds the filter and sort controls together |
| `ListFilter` | Renders the search input |
| `ListSort` | Renders the sort toggle button |
| `FlatList` | Renders the actual scrollable list of items |

> 📌 **TEACHER NOTE:** This is a great moment to introduce the idea of
> **Single Responsibility** — each component does exactly one thing. If a
> component is doing more than one thing, it's a signal to split it.
> This connects directly to the design principles we'll cover later.

---

### Building It — Step by Step

#### Step 1 — `ListContainer.js` (the brain)

This component owns all the state and passes it down.

```jsx
// ListContainer.js
import { useState } from 'react';
import List from './List';

// Our local data — 100 items
const allItems = new Array(100)
  .fill(null)
  .map((_, i) => ({ key: i.toString(), value: `Item ${i}` }));

// Helper function: filter and sort the data
function filterAndSort(data, filterText, ascending) {
  return data
    .filter(item =>
      filterText.length === 0 || item.value.includes(filterText)
    )
    .sort((a, b) => {
      if (ascending) {
        return a.value > b.value ? 1 : a.value === b.value ? 0 : -1;
      } else {
        return b.value > a.value ? 1 : a.value === b.value ? 0 : -1;
      }
    });
}

export default function ListContainer() {
  const [filter, setFilter] = useState('');
  const [ascending, setAscending] = useState(true);

  // Derive the displayed data from state
  const data = filterAndSort(allItems, filter, ascending);

  return (
    <List
      data={data}
      ascending={ascending}
      onFilter={text => setFilter(text)}
      onSort={() => setAscending(prev => !prev)}
    />
  );
}
```

> 📌 **TEACHER NOTE:** Notice we don't store `data` in state. We derive it
> from `allItems` using the filter and sort state. This is a React best practice —
> avoid storing data that can be computed from other state. Students know this
> from web React already.

---

#### Step 2 — `List.js` (the display layer)

Receives everything as props and passes them to the right sub-components.

```jsx
// List.js
import { View, FlatList, StyleSheet, Platform, StatusBar } from 'react-native';
import ListControls from './ListControls';
import ListItem from './ListItem';

export default function List({ data, ascending, onFilter, onSort }) {
  return (
    <View style={styles.container}>
      <FlatList
        data={data}
        renderItem={({ item }) => <ListItem item={item} />}
        ListHeaderComponent={
          <ListControls
            ascending={ascending}
            onFilter={onFilter}
            onSort={onSort}
          />
        }
      />
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

> 📌 **TEACHER NOTE:** Introduce `ListHeaderComponent` here. This is a FlatList
> prop that renders a component pinned to the top of the list — above the
> scrollable items. It's perfect for search bars and sort controls because it
> scrolls with the list but always starts at the top.

---

#### Step 3 — `ListItem.js` (how each row looks)

```jsx
// ListItem.js
import { Text, StyleSheet } from 'react-native';

export default function ListItem({ item }) {
  return (
    <Text style={styles.item}>{item.value}</Text>
  );
}

const styles = StyleSheet.create({
  item: {
    margin: 5,
    padding: 10,
    color: 'slategrey',
    backgroundColor: 'ghostwhite',
    textAlign: 'center',
    fontSize: 16,
  },
});
```

---

#### Step 4 — `ListControls.js` (the toolbar)

```jsx
// ListControls.js
import { View, StyleSheet } from 'react-native';
import ListFilter from './ListFilter';
import ListSort from './ListSort';

export default function ListControls({ ascending, onFilter, onSort }) {
  return (
    <View style={styles.controls}>
      <ListFilter onFilter={onFilter} />
      <ListSort ascending={ascending} onSort={onSort} />
    </View>
  );
}

const styles = StyleSheet.create({
  controls: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 10,
    backgroundColor: '#f0f0f0',
  },
});
```

---

#### Step 5 — `ListFilter.js` (the search input)

```jsx
// ListFilter.js
import { TextInput, StyleSheet } from 'react-native';

export default function ListFilter({ onFilter }) {
  return (
    <TextInput
      style={styles.input}
      placeholder="Search..."
      onChangeText={text => onFilter(text)}
      autoCorrect={false}
    />
  );
}

const styles = StyleSheet.create({
  input: {
    flex: 1,
    padding: 8,
    backgroundColor: 'white',
    borderWidth: 1,
    borderColor: '#ccc',
    borderRadius: 6,
    marginRight: 8,
    fontSize: 16,
  },
});
```

> 📌 **TEACHER NOTE:** `TextInput` is the React Native equivalent of `<input>`
> in HTML. The `onChangeText` prop fires every time the user types a character
> — it passes the current full string, not an event object. So no need for
> `e.target.value` like in web React. Just use the value directly.

---

#### Step 6 — `ListSort.js` (the sort toggle button)

```jsx
// ListSort.js
import { Button, View } from 'react-native';

export default function ListSort({ ascending, onSort }) {
  return (
    <View>
      <Button
        title={ascending ? '▲ Asc' : '▼ Desc'}
        onPress={onSort}
      />
    </View>
  );
}
```

---

#### Step 7 — Wire it up in `App.js`

```jsx
// App.js
import ListContainer from './ListContainer';

export default function App() {
  return <ListContainer />;
}
```

That's it. `App.js` stays clean — it just mounts the container.

---

<a name="part-3"></a>
## Part 3 — Fetching List Data from an API

### The Good News

In web React you fetched data using `fetch()` and `useEffect`. In React Native
you do **exactly the same thing**. React Native polyfills `fetch()` so it works
identically to what you already know.

```jsx
// This code works the same in web React AND React Native
useEffect(() => {
  fetch('https://api.example.com/items')
    .then(res => res.json())
    .then(data => setItems(data));
}, []);
```

No new API to learn. Same pattern, same hooks.

> 📌 **TEACHER NOTE:** This is a confidence moment for students. Emphasise that
> everything they learned about async data in web React transfers directly.
> fetch, async/await, useEffect, useState — all identical.

---

### Simulating an API with a Mock

Before connecting to a real API, it's good practice to build a **mock** — a
function that behaves like a real API (returns a Promise) but uses local data.
This lets you build and test the UI without needing a backend.

```jsx
// api.js — our mock API

const allItems = new Array(100)
  .fill(null)
  .map((_, i) => `Item ${i}`);

function filterAndSort(data, filterText, ascending) {
  return data
    .filter(item =>
      filterText.length === 0 || item.includes(filterText)
    )
    .sort((a, b) => {
      if (ascending) {
        return a > b ? 1 : a === b ? 0 : -1;
      } else {
        return b > a ? 1 : a === b ? 0 : -1;
      }
    });
}

// This function looks and behaves like a real fetch() call
// It returns a Promise that resolves with the filtered/sorted data
export async function fetchItems(filterText = '', ascending = true) {
  // In a real app, this would be:
  // const res = await fetch(`https://api.example.com/items?filter=${filterText}`);
  // const data = await res.json();

  // For now, we simulate the delay and response:
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(filterAndSort(allItems, filterText, ascending));
    }, 300); // simulate a 300ms network delay
  });
}
```

> 📌 **TEACHER NOTE:** The `setTimeout` of 300ms is important to include.
> It simulates a real network delay so students can see loading states in action.
> Without any delay, the data appears instantly and it's hard to demonstrate
> loading spinners or skeleton screens later.

---

### Updating ListContainer to Use the Mock API

Now we update `ListContainer` to fetch data instead of using local data directly:

```jsx
// ListContainer.js — updated to use the API
import { useState, useEffect } from 'react';
import { fetchItems } from './api';
import List from './List';

export default function ListContainer() {
  const [filter, setFilter] = useState('');
  const [ascending, setAscending] = useState(true);
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);

  // Helper to format items for FlatList (needs key + value)
  function formatItems(items) {
    return items.map((value, i) => ({
      key: i.toString(),
      value,
    }));
  }

  // Load data on mount
  useEffect(() => {
    fetchItems(filter, ascending)
      .then(items => {
        setData(formatItems(items));
        setLoading(false);
      });
  }, []);

  // Called when the user types in the search box
  function handleFilter(text) {
    setFilter(text);
    fetchItems(text, ascending)
      .then(items => setData(formatItems(items)));
  }

  // Called when the user taps the sort button
  function handleSort() {
    const newAscending = !ascending;
    setAscending(newAscending);
    fetchItems(filter, newAscending)
      .then(items => setData(formatItems(items)));
  }

  return (
    <List
      data={data}
      loading={loading}
      ascending={ascending}
      onFilter={handleFilter}
      onSort={handleSort}
    />
  );
}
```

---

### Adding a Loading Indicator

While data is loading, show an `ActivityIndicator` (React Native's built-in
spinner) instead of an empty screen:

```jsx
// List.js — updated with loading state
import { View, FlatList, ActivityIndicator, StyleSheet,
         Platform, StatusBar } from 'react-native';
import ListControls from './ListControls';
import ListItem from './ListItem';

export default function List({ data, loading, ascending, onFilter, onSort }) {
  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" color="#0000ff" />
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <FlatList
        data={data}
        renderItem={({ item }) => <ListItem item={item} />}
        ListHeaderComponent={
          <ListControls
            ascending={ascending}
            onFilter={onFilter}
            onSort={onSort}
          />
        }
      />
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
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

> 📌 **TEACHER NOTE:** `ActivityIndicator` is the native spinning loading
> indicator. On iOS it renders an iOS-style spinner. On Android it renders
> an Android-style spinner. You get the right native look automatically.
> This is a good example of "native experience first" — the right platform
> UI for free.

---

<a name="part-4"></a>
## Part 4 — Lazy List Loading (Infinite Scroll)

### What Is Lazy Loading?

So far our list loads all data at once. But imagine a social media feed with
thousands of posts. Loading all of them at once would be:
- Slow (large network request)
- Memory-heavy (all data in RAM at once)
- Wasteful (user may never scroll that far)

**Lazy loading** (also called infinite scroll) solves this by loading data
**in chunks** — only fetching the next batch when the user reaches the bottom
of the current list.

```
Initial load:         User scrolls to bottom:    And again:
┌──────────┐          ┌──────────┐               ┌──────────┐
│ Item 1   │          │ Item 1   │               │ Item 1   │
│ Item 2   │          │ Item 2   │               │ Item 2   │
│ Item 3   │          │ Item 3   │               │ ...      │
│ Item 4   │  scroll  │ Item 4   │  scroll       │ Item 20  │
│ Item 5   │ ──────►  │ Item 5   │ ──────►       │ Item 21  │
│ ...      │          │ ...      │               │ Item 22  │
│ Item 20  │          │ Item 20  │               │ ...      │
└──────────┘          │ Item 21  │               │ Item 40  │
  fetch 20            │ Item 22  │               └──────────┘
                      │ ...      │                 fetch 20 more
                      │ Item 40  │
                      └──────────┘
                        fetch 20 more
```

Real world examples: Instagram feed, Twitter/X, Facebook, TikTok, Jumia
product listings — they all use this pattern.

---

### The Key FlatList Prop: `onEndReached`

FlatList has a built-in prop for this exact use case:

```jsx
<FlatList
  data={data}
  renderItem={({ item }) => ...}
  onEndReached={loadMoreItems}        // called when user reaches the bottom
  onEndReachedThreshold={0.5}         // trigger when 50% from the bottom
/>
```

`onEndReachedThreshold` controls how early to trigger the load:
- `0` = trigger exactly at the bottom
- `0.5` = trigger when 50% of the remaining list is visible
- `1` = trigger when the end is one full screen away

Using `0.5` is a good default — it gives you time to fetch before the user
actually hits the very bottom.

---

### Building the Updated Mock API

The mock now generates items on demand — each call returns the next 20 items:

```jsx
// api.js — updated for lazy loading

let counter = 0; // tracks how many items we've generated so far

export function resetItems() {
  counter = 0; // useful for testing
}

// Each call to fetchItems() returns the NEXT 20 items
export async function fetchItems() {
  return new Promise(resolve => {
    setTimeout(() => {
      const newItems = new Array(20)
        .fill(null)
        .map(() => `Item ${counter++}`);
      resolve(newItems);
    }, 500);
  });
}
```

---

### The Updated ListContainer for Lazy Loading

The key difference from before: instead of **replacing** data, we **append** to it.

```jsx
// ListContainer.js — lazy loading version
import { useState, useEffect } from 'react';
import { fetchItems } from './api';
import List from './List';

export default function ListContainer() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [loadingMore, setLoadingMore] = useState(false);

  // Format items for FlatList
  function formatItems(items, startIndex) {
    return items.map((value, i) => ({
      key: (startIndex + i).toString(),
      value,
    }));
  }

  // Initial load on mount
  useEffect(() => {
    fetchItems().then(items => {
      setData(formatItems(items, 0));
      setLoading(false);
    });
  }, []);

  // Called when user reaches the end of the list
  function loadMore() {
    if (loadingMore) return; // prevent multiple calls at once

    setLoadingMore(true);
    fetchItems().then(items => {
      setData(prev => [
        ...prev,                              // keep existing items
        ...formatItems(items, prev.length),   // append new items
      ]);
      setLoadingMore(false);
    });
  }

  return (
    <List
      data={data}
      loading={loading}
      loadingMore={loadingMore}
      onEndReached={loadMore}
    />
  );
}
```

> 📌 **TEACHER NOTE:** The critical line is:
> `setData(prev => [...prev, ...formatItems(items, prev.length)])`
>
> We spread the **previous data** first, then the new items after it.
> This APPENDS rather than REPLACES. If you just did `setData(formatItems(items))`
> you would replace all existing items with only the new 20 — the list would
> reset every time the user scrolls. That's a very common mistake.

---

### The Updated List Component

```jsx
// List.js — lazy loading version
import { View, FlatList, ActivityIndicator, Text,
         StyleSheet, Platform, StatusBar } from 'react-native';
import ListItem from './ListItem';

export default function List({ data, loading, loadingMore, onEndReached }) {

  if (loading) {
    return (
      <View style={styles.loadingContainer}>
        <ActivityIndicator size="large" color="#0000ff" />
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <FlatList
        data={data}
        renderItem={({ item }) => <ListItem item={item} />}
        onEndReached={onEndReached}
        onEndReachedThreshold={0.5}
        ListFooterComponent={
          loadingMore
            ? <ActivityIndicator
                size="small"
                color="#999"
                style={{ padding: 16 }}
              />
            : null
        }
      />
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
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

> 📌 **TEACHER NOTE:** Two FlatList props to highlight here:
>
> - `ListHeaderComponent` — renders something pinned at the TOP of the list
>   (we used this for the filter/sort controls earlier)
> - `ListFooterComponent` — renders something at the BOTTOM of the list
>   (we use this for the "loading more" spinner)
>
> These are the correct places for these UI elements because they scroll
> with the list and stay in the right position automatically.

---

### How It Looks End to End

```
App.js
  └── ListContainer (owns state, fetches data)
        └── List (renders the FlatList)
              ├── [ListHeaderComponent] ← search/sort controls (Part 2)
              ├── ListItem ← each row
              ├── ListItem
              ├── ListItem
              ├── ...
              └── [ListFooterComponent] ← "loading more" spinner
```

---

## FlatList Props Reference

| Prop | Type | What It Does |
|---|---|---|
| `data` | Array | The data source |
| `renderItem` | Function | How to render each item |
| `ListHeaderComponent` | Component | Rendered at the top (above items) |
| `ListFooterComponent` | Component | Rendered at the bottom (below items) |
| `ListEmptyComponent` | Component | Rendered when `data` is empty |
| `onEndReached` | Function | Called when user scrolls to the end |
| `onEndReachedThreshold` | Number | How close to end before triggering (0–1) |
| `keyExtractor` | Function | Alternative to `key` field on data objects |
| `ItemSeparatorComponent` | Component | Rendered between each item |
| `refreshing` | Boolean | For pull-to-refresh — shows spinner |
| `onRefresh` | Function | For pull-to-refresh — called on pull |

---

### Bonus: `keyExtractor` — Alternative to the `key` Field

Instead of putting a `key` property on every data object, you can use
`keyExtractor` to tell FlatList how to generate a key:

```jsx
// Instead of this data shape:
// { key: '0', value: 'Item 0' }

// You can use this data shape:
// { id: 1, name: 'Product A', price: 5000 }

<FlatList
  data={products}
  keyExtractor={item => item.id.toString()}  // FlatList generates keys itself
  renderItem={({ item }) => <Text>{item.name}</Text>}
/>
```

This is useful when your data comes from an API and already has an `id` field —
you don't need to reshape it just to add a `key`.

---

<a name="exercises"></a>
## Chapter 17 Exercises

### Exercise 1 — Basic FlatList
Create a FlatList that renders a list of 50 Nigerian states and cities
(you can make them up or use real ones). Style each item with a bottom border
to visually separate the rows.

### Exercise 2 — Empty State
Add a `ListEmptyComponent` to your FlatList that shows a message like
"No items found" when the list has no data. Test it by setting your data
array to `[]`.

### Exercise 3 — Search Filter
Build a list of 20 product names. Add a `TextInput` above the list that
filters the items as the user types. The filter should be case-insensitive.

### Exercise 4 — Pull to Refresh
Add pull-to-refresh to a FlatList using the `refreshing` and `onRefresh` props.
When the user pulls down, simulate a refresh by waiting 1 second (setTimeout)
and then updating the data with a reshuffled version of the list.

### Exercise 5 — Infinite Scroll (Advanced)
Build a "news feed" that starts with 10 items. Each time the user reaches the
bottom, load 10 more items and append them to the list. Show a small spinner
at the bottom while new items are loading.

---

<a name="quick-reference"></a>
## Quick Reference

### Minimum FlatList Setup

```jsx
<FlatList
  data={myArray}
  keyExtractor={item => item.id.toString()}
  renderItem={({ item }) => <Text>{item.name}</Text>}
/>
```

### FlatList with Everything

```jsx
<FlatList
  data={data}
  keyExtractor={item => item.id.toString()}
  renderItem={({ item }) => <ListItem item={item} />}
  ListHeaderComponent={<SearchBar />}
  ListFooterComponent={loadingMore ? <ActivityIndicator /> : null}
  ListEmptyComponent={<Text>No results found</Text>}
  ItemSeparatorComponent={() => <View style={styles.separator} />}
  onEndReached={loadMoreItems}
  onEndReachedThreshold={0.5}
  refreshing={refreshing}
  onRefresh={handleRefresh}
/>
```

### Web React vs React Native — Lists

| Concept | Web React | React Native |
|---|---|---|
| List container | `<ul>` | `<FlatList>` |
| List item | `<li key={id}>` | `renderItem` prop |
| Unique key | `key` prop on `<li>` | `key` field in data or `keyExtractor` |
| Search input | `<input type="text">` | `<TextInput>` |
| Get input value | `e.target.value` | value passed directly to `onChangeText` |
| Loading spinner | custom CSS or library | `<ActivityIndicator>` |
| Infinite scroll | IntersectionObserver or library | `onEndReached` prop on FlatList |

---

*Chapter 17 — React Native Lists. Updated to current React Native and Expo standards.*
*`PropTypes` omitted — not recommended for new JSX projects.*
*`ListView` references removed — replaced by `FlatList` in React Native 0.43+.*
