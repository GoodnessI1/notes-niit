# Chapter 23: Images and Icons

## Overview

In this chapter, you'll learn how to work with the React Native `Image` component — loading images from different sources, resizing them dynamically, and implementing lazy loading with a placeholder pattern. You'll also learn how to render icons using `@expo/vector-icons`.

We'll build the image examples around a KarmaSwap-style use case (loading and resizing item listing photos), since that's a pattern you'll hit constantly in a marketplace app. The icon example stays generic — a browsable icon reference — since that's genuinely useful as-is.

---

## 1. Loading Images

The `<Image>` component renders image data the same way any other component renders — you just need to give it a `source`. There are two ways to do that:

- **Remote image** — pass an object with a `uri` property: `source={{ uri: "https://..." }}`
- **Local/bundled image** — pass the result of `require()`: `source={require("./assets/photo.png")}`

📝 **Note:** The original NIIT material defines prop validation with `PropTypes.oneOfType([...]).isRequired`. We're skipping `PropTypes` entirely per our convention — destructure props with sensible defaults instead.

```jsx
// ListingImage.jsx
import React from "react";
import { View, Image } from "react-native";
import styles from "./styles";

export default function ListingImage({
  primaryPhoto = { uri: "https://karmaswap.example.com/assets/placeholder-item.png" },
  fallbackPhoto = require("./assets/no-photo.png"),
}) {
  return (
    <View style={styles.container}>
      <Image style={styles.image} source={primaryPhoto} />
      <Image style={styles.image} source={fallbackPhoto} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flexDirection: "row",
    gap: 12,
  },
  image: {
    width: 120,
    height: 120,
    borderRadius: 8,
  },
});
```

**Real-world use case:** A KarmaSwap item listing typically has a seller-uploaded photo (remote, `uri`-based) and, if that fails to load or hasn't been set yet, a bundled "no photo available" placeholder (local, `require()`-based) — exactly the two source types shown above.

**Other options that exist:**
- **`expo-image`** — a drop-in replacement for core `Image` with built-in caching, blurhash placeholders, and better performance. Worth adopting once you outgrow the basics (see the callout at the end of this chapter).
- **`react-native-fast-image`** — an older but still widely used caching library, mostly superseded by `expo-image` in Expo projects now.

---

## 2. Resizing Images

The `width` and `height` style properties control how large an image renders. Let's build a small control that lets you resize an item photo dynamically — useful for previewing thumbnail vs. full-size rendering.

📝 **Note:** The original slide imports `Slider` from `"react-native"`. `Slider` was removed from React Native core years ago — it now lives in the community package `@react-native-community/slider`.

```bash
npx expo install @react-native-community/slider
```

```jsx
// ResizableListingPhoto.jsx
import React, { useState } from "react";
import { View, Text, Image, StyleSheet } from "react-native";
import Slider from "@react-native-community/slider";

export default function ResizableListingPhoto({
  source = require("./assets/sample-item.png"),
}) {
  const [width, setWidth] = useState(100);
  const [height, setHeight] = useState(100);

  return (
    <View style={styles.container}>
      <Image source={source} style={{ width, height }} />
      <Text>Width: {Math.round(width)}</Text>
      <Text>Height: {Math.round(height)}</Text>
      <Slider
        style={styles.slider}
        minimumValue={50}
        maximumValue={250}
        value={width}
        onValueChange={(value) => {
          setWidth(value);
          setHeight(value);
        }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    alignItems: "center",
    padding: 16,
  },
  slider: {
    width: 220,
    marginTop: 12,
  },
});
```

**Real-world use case:** Letting a seller preview how their listing photo will look as a thumbnail (small, in a browse grid) versus full-size (in the item detail screen) before confirming an upload.

**Other options that exist:**
- **`resizeMode` prop** (`cover`, `contain`, `stretch`, `center`) controls how the image fills its box — often more relevant than literally changing width/height, and worth mentioning alongside this.
- For production apps, image dimensions are more commonly computed from the container/grid layout (e.g. `Dimensions.get("window").width / numColumns`) rather than user-draggable sliders — the slider here is a nice teaching tool, not a typical UI pattern.

---

## 3. Lazy Image Loading

Sometimes an image is visible on screen immediately, but the network is slow to deliver it. Rather than showing empty space, you can render a placeholder until the real image finishes loading.

```jsx
// LazyListingImage.jsx
import React, { useState } from "react";
import { View, Image, StyleSheet } from "react-native";

const placeholder = require("./assets/photo-placeholder.png");

function Placeholder({ loaded, style }) {
  if (loaded) return null;
  return <Image style={style} source={placeholder} />;
}

export default function LazyListingImage({ style, ...imageProps }) {
  const [loaded, setLoaded] = useState(false);

  return (
    <View style={style}>
      <Placeholder loaded={loaded} style={style} />
      <Image
        {...imageProps}
        style={style}
        onLoad={() => setLoaded(true)}
      />
    </View>
  );
}
```

```jsx
// ItemPhotoLoader.jsx
import React, { useState } from "react";
import { View, Button, StyleSheet } from "react-native";
import LazyListingImage from "./LazyListingImage";

const remotePhoto =
  "https://karmaswap.example.com/uploads/items/blender-4521.jpg";

export default function ItemPhotoLoader() {
  const [source, setSource] = useState(null);

  return (
    <View style={styles.container}>
      <LazyListingImage
        style={{ width: 200, height: 150 }}
        resizeMode="contain"
        source={source}
      />
      <Button
        title="Load Item Photo"
        onPress={() => setSource({ uri: remotePhoto })}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    alignItems: "center",
    gap: 12,
    padding: 16,
  },
});
```

Until `source` is set, `LazyListingImage` receives `source={null}`, so `loaded` stays `false` and the placeholder shows. Once the button sets a real `uri`, the `<Image>` fires `onLoad`, `loaded` flips to `true`, and the placeholder unmounts.

📝 **Note:** We dropped `LazyImage.propTypes` from the original slide (the `PropTypes.shape({ width, height })` validation) — again, no `PropTypes` per convention.

**Real-world use case:** A KarmaSwap browse grid where item photos load progressively as the user scrolls — each `LazyListingImage` shows a generic placeholder until its specific photo arrives from the network, rather than leaving blank tiles.

**Other options that exist:**
- **`expo-image`** handles this automatically — it has a built-in `placeholder` prop (including blurhash support) and doesn't require you to hand-roll this pattern at all.
- **`FlatList`'s `onEndReached`** combined with pagination is the more complete real-world answer to "loading images as the user scrolls" — lazy-loading individual images (this section) and lazy-loading list *data* (pagination) are complementary, not the same thing.

---

## 4. Rendering Icons

Icons are just as useful in a mobile UI as on the web — indicating actions, categories, and states without relying purely on text.

📝 **Note:** The original slide has a naming inconsistency — it references the `react-native-vector-icons` package by name but shows the install command and import path for `@expo/vector-icons`. In an Expo-managed project, **`@expo/vector-icons` is what you actually want** — it's bundled with the Expo SDK by default, so there's usually nothing to install at all.

```jsx
// IconBrowser.jsx
import React, { useState, useEffect } from "react";
import { View, FlatList, Text, StyleSheet } from "react-native";
import { FontAwesome } from "@expo/vector-icons";
import { Picker } from "@react-native-picker/picker";
import iconNames from "./icon-names.json";

export default function IconBrowser() {
  const [selected, setSelected] = useState("Web Application Icons");
  const [listSource, setListSource] = useState([]);
  const categories = Object.keys(iconNames);

  function updateListSource(category) {
    setListSource(iconNames[category]);
    setSelected(category);
  }

  useEffect(() => {
    updateListSource(selected);
  }, []);

  return (
    <View style={styles.container}>
      <View style={styles.pickerWrapper}>
        <Picker selectedValue={selected} onValueChange={updateListSource}>
          {categories.map((category) => (
            <Picker.Item key={category} label={category} value={category} />
          ))}
        </Picker>
      </View>
      <FlatList
        style={styles.icons}
        data={listSource.map((value, key) => ({ key: key.toString(), value }))}
        renderItem={({ item }) => (
          <View style={styles.item}>
            <FontAwesome name={item.value} style={styles.itemIcon} />
            <Text style={styles.itemText}>{item.value}</Text>
          </View>
        )}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  pickerWrapper: {
    borderBottomWidth: 1,
    borderBottomColor: "#e0e0e0",
  },
  icons: {
    flex: 1,
  },
  item: {
    flexDirection: "row",
    alignItems: "center",
    padding: 12,
    gap: 12,
  },
  itemIcon: {
    fontSize: 20,
  },
  itemText: {
    fontSize: 16,
  },
});
```

📝 **Note:** `Picker` is imported from `@react-native-picker/picker` here, consistent with how we handled it back in Chapter 20 — not from core `react-native`, where it no longer exists.

**Real-world use case:** A category picker for browsing FontAwesome icons is a fine teaching example as-is — you'd use this exact pattern (icon name driven by a data source, rendered via `FlatList`) for something like a KarmaSwap category filter bar, just swapping `iconNames` for your own category-to-icon mapping.

**Other options that exist:**
- **`@expo/vector-icons`** actually bundles several icon families beyond FontAwesome — `Ionicons`, `MaterialIcons`, `MaterialCommunityIcons`, `Feather`, `AntDesign`, and more, all imported the same way.
- **Custom SVG icons** via `react-native-svg` — worth it once your app needs icons outside the standard font-icon sets (e.g. a custom brand mark).

---

## A Note on `expo-image`

Every section above uses core `Image`, matching the original material — and it's a perfectly good component. But if you're starting a new Expo project today, it's worth knowing that **`expo-image`** exists as a modern drop-in replacement:

```bash
npx expo install expo-image
```

```jsx
import { Image } from "expo-image";

<Image
  source={{ uri: remotePhoto }}
  style={{ width: 200, height: 150 }}
  placeholder={blurhash}
  contentFit="contain"
  transition={300}
/>
```

It handles disk/memory caching, blurhash placeholders, and smooth load transitions out of the box — meaning the hand-rolled `LazyListingImage` pattern from Section 3 becomes largely unnecessary once you adopt it. We covered the manual pattern first because understanding *why* it works is useful, even if `expo-image` does it for you in practice.

---

## Summary

In this chapter, you learned how to work with images and icons in React Native:

- **Loading images** from both remote (`uri`) and local (`require()`) sources
- **Resizing images** dynamically using `width`/`height` styles, with `@react-native-community/slider` replacing the removed core `Slider`
- **Lazy loading images** with a placeholder-until-loaded pattern — and where `expo-image` replaces the need for it
- **Rendering icons** with `@expo/vector-icons`, using `@react-native-picker/picker` for the category selector

---

## Exercises

1. **Basic:** Build a `SellerAvatar` component that loads a seller's profile photo from a `uri`, falling back to a bundled default avatar image if `photoUrl` is not provided.

2. **Intermediate:** Extend `ResizableListingPhoto` so the width and height can be adjusted independently (two sliders instead of one), and add a `resizeMode` picker (`cover` / `contain` / `stretch`) so students can see how each mode affects a non-square image.

3. **Intermediate:** Modify `LazyListingImage` to show a loading spinner (`ActivityIndicator`, from Chapter 18) instead of a static placeholder image while the photo is loading.

4. **Advanced:** Build a `CategoryIconGrid` component that renders KarmaSwap trade categories (e.g. Electronics, Furniture, Books, Services) as a grid of icon + label cards using `@expo/vector-icons`, where tapping a card calls an `onSelectCategory(category)` callback.

5. **Advanced:** Rebuild the `IconBrowser` example using `expo-image` for any thumbnail previews of the icons' host app, and add a search `TextInput` (Chapter 20) that filters `listSource` by icon name as the user types.
