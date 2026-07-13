# Chapter 19: Location and Maps in React Native

## 1. Why Do We Need Location?

Think about how many apps you use daily that quietly depend on knowing where you are: a food delivery app estimating your delivery time, a weather app showing today's forecast without you typing a city name, a ride-hailing app matching you with the nearest driver, a fitness app tracking your running route.

Location isn't just a "nice to have" feature — for a huge class of apps, it's the thing that makes the app *useful at all*. An app that asks "where do you want your package delivered?" every single time, instead of just knowing, feels broken by comparison.

React Native gives us two complementary tools for this:
- **Geolocation** — figuring out *where the device physically is* (coordinates, and optionally a human-readable address).
- **Maps** (`react-native-maps`) — visually rendering that location, along with other points of interest, on an actual map.

> 💡 **Tip:** Location and maps are often used together, but they're separate concerns. You can have geolocation without a map (e.g., tagging a photo with coordinates), and you can have a map without live geolocation (e.g., a static map showing a store's address).

---

## 2. When Should It Be Used?

| Use Case | Why Location Helps |
|---|---|
| Delivery / logistics apps | Show estimated arrival, track driver/courier in real time |
| Ride-hailing / navigation apps | Match nearest available driver, calculate routes |
| Social / check-in apps | Tag posts with a location, show "nearby" content |
| Retail / store-locator apps | Show the nearest branch, calculate distance |
| Fitness / running apps | Track a route, calculate distance/pace |
| Weather apps | Auto-detect city instead of manual entry |
| Event / venue apps | Show attendees the way to points of interest on-site |

> 💡 **Tip:** Not every app needs location. Before reaching for `expo-location`, ask: *does knowing where the user is actually change what I show them or what I do?* If the answer is no, you probably don't need it — and asking for a permission you don't use is a fast way to make users distrust your app.

---

## 3. What Effective Use of Location Looks Like

Before writing any code, a few things need to be in place — location features fail fast in class (and in production) if these aren't sorted first.

### 3.1 What You'll Need

**Install the package (Expo-managed workflow):**

```bash
npx expo install expo-location
```

**Configure permissions in `app.json`:**

```json
{
  "expo": {
    "plugins": [
      [
        "expo-location",
        {
          "locationAlwaysAndWhenInUsePermission": "Allow $(PRODUCT_NAME) to use your location."
        }
      ]
    ]
  }
}
```

**Request permission at runtime, before requesting a position:**

```jsx
import * as Location from "expo-location";

const { status } = await Location.requestForegroundPermissionsAsync();
if (status !== "granted") {
  // handle the user declining — don't silently fail
}
```

> 💡 **Tip:** Always request permission *in response to a user action* (e.g., tapping "Find my location"), not automatically on app launch. It reads as far less intrusive, and it gives you a natural moment to explain *why* you need it.

### 3.2 Principles for Effective Use

- **Ask, don't assume.** Always check permission status before requesting a position — never assume it was granted.
- **Handle denial gracefully.** If the user says no, don't leave the screen blank — explain what feature they're missing and let them continue.
- **Prefer `getCurrentPositionAsync` over `watchPositionAsync` unless you need continuous tracking.** Watching position drains battery; only do it when the use case genuinely needs live updates (e.g., turn-by-turn navigation).
- **Always clean up a watcher.** A `watchPositionAsync` subscription needs to be removed when a component unmounts, or you'll leak location updates.
- **Don't hardcode API keys in your source.** The `API_KEY` used for reverse geocoding (see below) should live in an environment variable, not committed to your repo.

---

## 4. Scenario 1: "Where Am I?" — Getting the Device's Location

This example fetches the device's coordinates, then uses those coordinates to look up a human-readable address via the Google Maps Geocoding API.

> 📝 **Setup note:** This requires a Google Cloud API key with the **Geocoding API** enabled, and billing set up on that project (Google requires a billing account even within the free tier). Generate the key in the Google Cloud Console, then paste it into `API_KEY` below — ideally loaded from an environment variable rather than hardcoded.

```jsx
import React, { useState, useEffect } from "react";
import { Text, View } from "react-native";
import * as Location from "expo-location";
import styles from "./styles";

const API_KEY = ""; // your Google Maps Geocoding API key
const URL = "https://maps.googleapis.com/maps/api/geocode/json?latlng=";

export default function WhereAmI() {
  const [address, setAddress] = useState("loading...");
  const [latitude, setLatitude] = useState(null);
  const [longitude, setLongitude] = useState(null);

  useEffect(() => {
    let subscription;

    async function startWatching() {
      const { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== "granted") {
        setAddress("Location permission denied");
        return;
      }

      async function setPosition({ coords: { latitude, longitude } }) {
        setLatitude(latitude);
        setLongitude(longitude);

        try {
          const resp = await fetch(`${URL}${latitude},${longitude}&key=${API_KEY}`);
          const { results } = await resp.json();
          setAddress(results[0]?.formatted_address ?? "Address not found");
        } catch (e) {
          console.error(e);
        }
      }

      const current = await Location.getCurrentPositionAsync({});
      setPosition(current);

      subscription = await Location.watchPositionAsync(
        { accuracy: Location.Accuracy.High },
        setPosition
      );
    }

    startWatching();

    return () => {
      subscription?.remove();
    };
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.label}>Address: {address}</Text>
      <Text style={styles.label}>Latitude: {latitude}</Text>
      <Text style={styles.label}>Longitude: {longitude}</Text>
    </View>
  );
}
```

**What's happening:**
- `getCurrentPositionAsync` runs once, immediately, to get an initial fix.
- `watchPositionAsync` calls `setPosition` again any time the device moves, keeping the address up to date.
- The returned `subscription` is cleaned up in the effect's return function — this is the modern equivalent of `clearWatch()`.

**Real-world use case:** A delivery app auto-filling the "delivery address" field with the user's current location instead of requiring manual entry.

---

## 5. Scenario 2: "What's Around Me?" — Rendering a Map

`MapView` from `react-native-maps` is the core component for rendering maps.

**Setup (Expo-managed workflow):**

```bash
npx expo install react-native-maps
```

```json
{
  "expo": {
    "plugins": ["react-native-maps"]
  }
}
```

```jsx
import React from "react";
import { View } from "react-native";
import MapView from "react-native-maps";
import styles from "./styles";

export default function WhatsAroundMe() {
  return (
    <View style={styles.container}>
      <MapView
        style={styles.mapView}
        showsUserLocation
        followUserLocation
      />
    </View>
  );
}
```

- `showsUserLocation` activates the blue dot marking the device's physical location.
- `followUserLocation` keeps the map centered/zoomed on the user as they move — generally worth pairing with `showsUserLocation` so the map actually zooms to where the user is, rather than defaulting to a zoomed-out world view.

By default, points of interest (shops, parks, landmarks) are already rendered on the map by the underlying native map provider.

**Real-world use case:** A store-locator screen that opens already centered on the user, so they immediately see how far they are from the nearest branch.

---

## 6. Scenario 3: Annotating Points of Interest

Annotations layer extra information on top of the base map — either specific point **markers**, or shaded **region overlays**.

### 6.1 Plotting Points (Markers)

```jsx
import React from "react";
import { View } from "react-native";
import MapView, { Marker } from "react-native-maps";
import styles from "./styles";

export default function App() {
  return (
    <View style={styles.container}>
      <MapView
        style={styles.mapView}
        showsPointsOfInterest={false}
        showsUserLocation
        followUserLocation
      >
        <Marker
          title="Duff Brewery"
          description="Duff beer for me, Duff beer for you"
          coordinate={{ latitude: 43.8418728, longitude: -79.086082 }}
        />
        <Marker
          title="Pawtucket Brewery"
          description="New! Patriot Light!"
          coordinate={{ latitude: 43.8401328, longitude: -79.085407 }}
        />
      </MapView>
    </View>
  );
}
```

Setting `showsPointsOfInterest={false}` opts out of the map's default businesses/landmarks layer — useful when you want *only your own* markers to stand out, without visual competition from the map provider's built-in icons.

**Real-world use case:** A brewery-crawl app plotting a curated list of venues, deliberately hiding unrelated points of interest so the user's attention stays on the curated list.

### 6.2 Plotting Overlays (Regions)

A marker is a single point. An overlay/region is a shape — a connect-the-dots outline of several coordinates — useful for highlighting an *area* rather than a single address.

```jsx
import React, { useState } from "react";
import { View, Text } from "react-native";
import MapView, { Polygon } from "react-native-maps";
import styles from "./styles";

const ipaRegion = {
  coordinates: [
    { latitude: 43.8486744, longitude: -79.0695283 },
    { latitude: 43.8537168, longitude: -79.0700046 },
    { latitude: 43.8518394, longitude: -79.0725697 },
    { latitude: 43.8481651, longitude: -79.0716377 },
    { latitude: 43.8486744, longitude: -79.0695283 }
  ],
  strokeColor: "coral",
  strokeWidth: 4
};

const stoutRegion = {
  coordinates: [
    { latitude: 43.8486744, longitude: -79.0693283 }
    // ...remaining coordinates
  ],
  strokeColor: "firebrick",
  strokeWidth: 4
};

export default function PlottingOverlays() {
  const [ipaStyles, setIpaStyles] = useState([styles.ipaText, styles.boldText]);
  const [stoutStyles, setStoutStyles] = useState([styles.stoutText]);
  const [overlays, setOverlays] = useState([ipaRegion]);

  function onClickIpa() {
    setIpaStyles([...ipaStyles, styles.boldText]);
    setStoutStyles([stoutStyles[0]]);
    setOverlays([ipaRegion]);
  }

  function onClickStout() {
    setStoutStyles([...stoutStyles, styles.boldText]);
    setIpaStyles([ipaStyles[0]]);
    setOverlays([stoutRegion]);
  }

  return (
    <View style={styles.container}>
      <View>
        <Text style={ipaStyles} onPress={onClickIpa}>IPA Fans</Text>
        <Text style={stoutStyles} onPress={onClickStout}>Stout Fans</Text>
      </View>
      <MapView
        style={styles.mapView}
        showsPointsOfInterest={false}
        showsUserLocation
        followUserLocation
      >
        {overlays.map((v, i) => (
          <Polygon
            key={i}
            coordinates={v.coordinates}
            strokeColor={v.strokeColor}
            strokeWidth={v.strokeWidth}
          />
        ))}
      </MapView>
    </View>
  );
}
```

Tapping "IPA Fans" or "Stout Fans" swaps which region is rendered, and bolds the active label — the region data itself is just a list of coordinates, while the rest of the logic is ordinary state handling for which region is currently selected.

**Real-world use case:** A food delivery app shading its active delivery zone on the map, so users immediately see whether their address falls inside it.

---

## 7. Exercises

1. **Geolocation basics:** Build a screen that requests location permission, and displays "Permission granted" or "Permission denied" depending on the user's response — without yet fetching a position.
2. **Where Am I:** Extend the `WhereAmI` example to also display the accuracy of the reading (`coords.accuracy`) alongside the address.
3. **Map basics:** Render a `MapView` centered on your own city (hint: use the `initialRegion` prop with a fixed latitude/longitude/delta) without live location tracking.
4. **Markers:** Plot three markers for your favorite local restaurants, each with a `title` and `description`.
5. **Overlays challenge:** Extend the brewery overlay example with a third region ("Cider Fans"), and refactor the repeated `onClickX` functions into a single reusable function parameterized by region.

---

## 8. Summary

- Location matters because it lets an app respond to *where the user actually is*, instead of asking them to describe it every time.
- Use it when it changes what you show or do — not just because it's available.
- Effective use starts before any code: install `expo-location`, configure permissions in `app.json`, and always request permission in response to a user action.
- **Geolocation** (`expo-location`) gets you coordinates — and optionally, via a geocoding API, a human-readable address.
- **Maps** (`react-native-maps`) visually render that location, with `showsUserLocation`/`followUserLocation` handling the common case out of the box.
- **Markers** highlight specific points; **overlays/regions** highlight areas — pick based on whether you're pointing at a place or shading a zone.
