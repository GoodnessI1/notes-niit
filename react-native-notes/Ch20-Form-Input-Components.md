# Chapter 20: Form Input Components in React Native

This chapter covers the React Native equivalents of familiar web form elements: text fields, dropdowns/selects, toggles, and date/time pickers. Each section below follows the same pattern: what the component is, the props/options you'll actually use, a modernized example, a real-world use case, and alternatives that exist.

---

## 1. Collecting Text Input — `<TextInput>`

There's more to a text input than just capturing keystrokes: should it have placeholder text? Is this sensitive data that shouldn't be visible on screen? Should you react to every keystroke, or only when the user finishes editing?

Unlike web `<input>` elements, `<TextInput>` also has to account for the device's **built-in virtual keyboard** — you can configure which keyboard layout appears and respond to keyboard-driven events like submission.

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `placeholder` | Gray hint text shown when the field is empty |
| `secureTextEntry` | Masks input (e.g., `••••••`) — for passwords |
| `keyboardType` | Changes the virtual keyboard layout: `default`, `numeric`, `email-address`, `phone-pad`, `decimal-pad` |
| `autoCapitalize` | Controls auto-capitalization: `none`, `sentences`, `words`, `characters` |
| `autoCorrect` | Enables/disables spelling autocorrect (`true`/`false`) |
| `maxLength` | Maximum number of characters allowed |
| `multiline` | Allows the input to grow into a multi-line text area |
| `editable` | Set to `false` to make the field read-only |
| `returnKeyType` | Changes the keyboard's return key label: `done`, `go`, `next`, `search`, `send` |
| `value` / `onChangeText` | Controlled input pattern — the standard way to bind text input to state |
| `onSubmitEditing` | Fires when the user presses the keyboard's return/submit key |
| `onFocus` / `onBlur` | Fire when the field gains/loses focus |

### Example

```jsx
// Input.js
import React from "react";
import { Text, TextInput, View } from "react-native";
import styles from "./styles";

export default function Input({ label, ...props }) {
  return (
    <View style={styles.textInputContainer}>
      <Text style={styles.textInputLabel}>{label}</Text>
      <TextInput style={styles.textInput} {...props} />
    </View>
  );
}
```

```jsx
// CollectingTextInput.js
import React, { useState } from "react";
import { Text, View } from "react-native";
import styles from "./styles";
import Input from "./Input";

export default function CollectingTextInput() {
  const [changedText, setChangedText] = useState("");
  const [submittedText, setSubmittedText] = useState("");

  return (
    <View style={styles.container}>
      <Input label="Basic Text Input:" />
      <Input label="Password Input:" secureTextEntry />
      <Input label="Email Input:" keyboardType="email-address" autoCapitalize="none" />
      <Input label="Return Key:" returnKeyType="search" />
      <Input label="Placeholder Text:" placeholder="Search" />
      <Input
        label="Input Events:"
        onChangeText={setChangedText}
        onSubmitEditing={e => setSubmittedText(e.nativeEvent.text)}
        onFocus={() => {
          setChangedText("");
          setSubmittedText("");
        }}
      />
      <Text>Changed: {changedText}</Text>
      <Text>Submitted: {submittedText}</Text>
    </View>
  );
}
```

> 💡 **Note:** `onChangeText` gives you the plain string directly (unlike web's `onChange`, which requires digging into `event.target.value`). `onSubmitEditing`, however, gives you an event object — the text lives at `e.nativeEvent.text`.

**Real-world use case:** A sign-up form with separate fields for email (`keyboardType="email-address"`, `autoCapitalize="none"`) and password (`secureTextEntry`), each triggering the next field via `returnKeyType="next"`.

**Other options that exist:**
- **`react-native-paper`'s `TextInput`** — Material Design styled, with built-in floating labels and validation states.
- **`react-hook-form`** paired with `<TextInput>` — for larger forms, handles validation and state management so you're not wiring up `useState` per field.

---

## 2. Selecting From a List of Options — `<Picker>`

On the web, you'd reach for `<select>`. In React Native, the equivalent is the **Picker** component — but it's no longer part of core React Native. You'll need the community-maintained package:

```bash
npx expo install @react-native-picker/picker
```

Unlike the original core `<Picker>`, this package handles both iOS and Android rendering internally, so you don't need to split your component into `.ios.js`/`.android.js` files — one component works everywhere.

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `selectedValue` | The currently selected value — pair with `onValueChange` for a controlled picker |
| `onValueChange` | Fires with the new value whenever the user picks a different option |
| `enabled` | Set to `false` to disable the picker |
| `mode` (Android only) | `dialog` (popup) or `dropdown` (inline) |
| `<Picker.Item label={} value={} />` | Each individual option — `label` is what's displayed, `value` is what gets passed to `onValueChange` |

### Example: A Reusable Select Component

```jsx
// Select.js
import React from "react";
import { View, Text } from "react-native";
import { Picker } from "@react-native-picker/picker";
import styles from "./styles";

export default function Select({ label, items, selectedValue, onValueChange }) {
  return (
    <View style={styles.pickerContainer}>
      <Text style={styles.pickerLabel}>{label}</Text>
      <Picker selectedValue={selectedValue} onValueChange={onValueChange}>
        {items.map(item => (
          <Picker.Item key={item.value} label={item.label} value={item.value} />
        ))}
      </Picker>
    </View>
  );
}
```

### Example: Cascading Pickers (Country → City)

This is a common real-world pattern: selecting a value in one picker changes the available options in a second.

```jsx
import React, { useState, useMemo } from "react";
import { View, Text } from "react-native";
import styles from "./styles";
import Select from "./Select";

const citiesByCountry = {
  Nigeria: ["Lagos", "Abuja", "Ibadan"],
  Canada: ["Toronto", "Vancouver", "Ottawa"],
  Kenya: ["Nairobi", "Mombasa", "Kisumu"]
};

const countries = Object.keys(citiesByCountry).map(c => ({ label: c, value: c }));

export default function SelectingFromAList() {
  const [country, setCountry] = useState(countries[0].value);
  const [city, setCity] = useState(citiesByCountry[countries[0].value][0]);

  const cityOptions = useMemo(
    () => citiesByCountry[country].map(c => ({ label: c, value: c })),
    [country]
  );

  function onCountryChange(value) {
    setCountry(value);
    setCity(citiesByCountry[value][0]); // reset city to the first valid option
  }

  return (
    <View style={styles.container}>
      <Select label="Country:" items={countries} selectedValue={country} onValueChange={onCountryChange} />
      <Select label="City:" items={cityOptions} selectedValue={city} onValueChange={setCity} />
      <Text>Selected: {city}, {country}</Text>
    </View>
  );
}
```

The key detail: whenever `country` changes, `city` is reset to a valid option for the new country — otherwise you could end up with a `city` value that doesn't exist in the new list.

**Real-world use case:** A shipping address form where selecting a country filters the available states/provinces, and selecting a state filters the available cities.

**Other options that exist:**
- **`react-native-modal-selector` / `react-native-dropdown-picker`** — community packages offering more customizable dropdown styling than the native `Picker` allows.
- **A custom `Modal` + `FlatList`** — for full design control (e.g., a searchable select), some teams skip `Picker` entirely and build their own selection modal.

---

## 3. Toggling Between On and Off — `<Switch>`

`<Switch>` is React Native's built-in on/off toggle, equivalent to a web checkbox but styled as a sliding switch. It works out of the box on both iOS and Android and is easier to style than `Picker`.

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `value` | Boolean — whether the switch is on or off (controlled component) |
| `onValueChange` | Fires with the new boolean value when toggled |
| `disabled` | Set to `true` to prevent interaction |
| `trackColor` | Object `{ false: color, true: color }` — background color for off/on states |
| `thumbColor` | Color of the sliding circle/thumb |

### Example: A Labeled Switch

```jsx
// LabeledSwitch.js
import React from "react";
import { View, Text, Switch } from "react-native";
import styles from "./styles";

export default function LabeledSwitch({ label, ...props }) {
  return (
    <View style={styles.customSwitch}>
      <Text>{label}</Text>
      <Switch {...props} />
    </View>
  );
}
```

### Example: Mutually Exclusive Switches

A realistic case for this mechanic: letting a user choose between two mutually exclusive input methods — enabling one should disable the other.

```jsx
import React, { useState } from "react";
import { View } from "react-native";
import styles from "./styles";
import LabeledSwitch from "./LabeledSwitch";

export default function TogglingOnAndOff() {
  const [useCurrentLocation, setUseCurrentLocation] = useState(false);
  const [enterManually, setEnterManually] = useState(false);

  return (
    <View style={styles.container}>
      <LabeledSwitch
        label="Use Current Location"
        value={useCurrentLocation}
        disabled={enterManually}
        onValueChange={setUseCurrentLocation}
      />
      <LabeledSwitch
        label="Enter Address Manually"
        value={enterManually}
        disabled={useCurrentLocation}
        onValueChange={setEnterManually}
      />
    </View>
  );
}
```

Each switch's `disabled` prop is tied to the *other* switch's `value` — turning one on locks the other out, which makes sense here since a delivery address can't simultaneously be "current location" and "manually entered."

**Real-world use case:** A privacy settings screen with a "Make Profile Public" switch that, when off, disables a dependent "Show Activity Status" switch (since activity status only matters if the profile is public).

**Other options that exist:**
- **`react-native-paper`'s `Switch`** — Material Design themed, matches a broader design system automatically.
- **A custom animated toggle** (via `Animated` or `react-native-reanimated`) — for fully custom visual designs beyond what `trackColor`/`thumbColor` allow.

---

## 4. Collecting Date/Time Input — `DateTimePicker`

The original core React Native had *separate* `DatePickerIOS` and `DatePickerAndroid` components, requiring you to hand-write the cross-platform differences yourself. Both have been removed from core React Native. The modern, actively maintained replacement is a single community package that handles both platforms:

```bash
npx expo install @react-native-community/datetimepicker
```

### Key Props You'll Use

| Prop | What It Does |
|---|---|
| `value` | The currently selected `Date` object |
| `mode` | `date`, `time`, or `datetime` |
| `display` | `default`, `spinner`, `calendar`, `clock` — rendering style (availability varies by platform) |
| `onChange` | Fires with `(event, selectedDate)` when the user picks a value |
| `minimumDate` / `maximumDate` | Restrict the selectable range |

> 📝 **Platform note:** On iOS, the picker can render inline (always visible). On Android, it renders as a modal dialog that opens on demand — this is why the Android usage pattern below shows/hides the picker with a boolean, while iOS can just render it directly.

### Example: DatePicker

```jsx
// DatePicker.js
import React, { useState } from "react";
import { Text, View, Platform, Pressable } from "react-native";
import DateTimePicker from "@react-native-community/datetimepicker";
import styles from "./styles";

export default function DatePicker({ label, date, onDateChange }) {
  const [visible, setVisible] = useState(false);

  function handleChange(event, selectedDate) {
    setVisible(false);
    if (selectedDate) onDateChange(selectedDate);
  }

  return (
    <View style={styles.datePickerContainer}>
      <Text style={styles.datePickerLabel}>{label}</Text>

      {Platform.OS === "ios" ? (
        <DateTimePicker mode="date" value={date} onChange={handleChange} />
      ) : (
        <>
          <Pressable onPress={() => setVisible(true)}>
            <Text>{date.toLocaleDateString()}</Text>
          </Pressable>
          {visible && (
            <DateTimePicker mode="date" value={date} onChange={handleChange} />
          )}
        </>
      )}
    </View>
  );
}
```

### Example: TimePicker

Same structure as `DatePicker`, with `mode="time"` and a time-formatted label:

```jsx
// TimePicker.js
import React, { useState } from "react";
import { Text, View, Platform, Pressable } from "react-native";
import DateTimePicker from "@react-native-community/datetimepicker";
import styles from "./styles";

export default function TimePicker({ label, date, onTimeChange }) {
  const [visible, setVisible] = useState(false);

  function handleChange(event, selectedTime) {
    setVisible(false);
    if (selectedTime) onTimeChange(selectedTime);
  }

  return (
    <View style={styles.datePickerContainer}>
      <Text style={styles.datePickerLabel}>{label}</Text>

      {Platform.OS === "ios" ? (
        <DateTimePicker mode="time" value={date} onChange={handleChange} />
      ) : (
        <>
          <Pressable onPress={() => setVisible(true)}>
            <Text>{date.toLocaleTimeString()}</Text>
          </Pressable>
          {visible && (
            <DateTimePicker mode="time" value={date} onChange={handleChange} />
          )}
        </>
      )}
    </View>
  );
}
```

### Using Both Together

```jsx
import React, { useState } from "react";
import { View } from "react-native";
import styles from "./styles";
import DatePicker from "./DatePicker";
import TimePicker from "./TimePicker";

export default function CollectingDateTimeInput() {
  const [date, setDate] = useState(new Date());
  const [time, setTime] = useState(new Date());

  return (
    <View style={styles.container}>
      <DatePicker label="Pick a date, any date:" date={date} onDateChange={setDate} />
      <TimePicker label="Pick a time, any time:" date={time} onTimeChange={setTime} />
    </View>
  );
}
```

**Real-world use case:** An appointment booking screen where the user picks a date, then a time slot, with `minimumDate={new Date()}` preventing them from booking in the past.

**Other options that exist:**
- **`react-native-modal-datetime-picker`** — wraps the same community package in a consistent modal presentation on both platforms, if you want iOS and Android to look/behave identically rather than embracing each platform's native convention.
- **A custom calendar component** (e.g., `react-native-calendars`) — for date-range selection or visual month views, which `DateTimePicker` doesn't support on its own.

---

## 5. Exercises

1. **TextInput:** Build a "Contact Form" with name, email (`keyboardType="email-address"`), and message (`multiline`) fields, displaying the entered values live as the user types.
2. **Picker:** Build a two-level cascading picker for "Category → Subcategory" (e.g., Electronics → Phones/Laptops/Headphones; Clothing → Shirts/Shoes/Hats).
3. **Switch:** Build a settings screen with three independent switches (not mutually exclusive) for "Push Notifications," "Email Updates," and "Dark Mode" — displaying a live summary of which are enabled.
4. **DateTimePicker:** Extend the appointment booking example with a `minimumDate` of today and a `maximumDate` 30 days from today.
5. **Challenge:** Combine all four components into a single "Event Registration" form: name (TextInput), event category (Picker), "Remind Me" toggle (Switch), and event date/time (DateTimePicker) — then render a summary of all the collected values.

---

## 6. Summary

- **`<TextInput>`** collects free-form text, with props like `secureTextEntry`, `keyboardType`, and `multiline` shaping both the keyboard and the behavior.
- **`<Picker>`** (via `@react-native-picker/picker`) is the equivalent of a web `<select>` — one component now handles both platforms, commonly used for cascading selections.
- **`<Switch>`** is a built-in on/off toggle — simple to style, and its `disabled` prop is often tied to another switch's `value` for mutually exclusive choices.
- **`DateTimePicker`** (via `@react-native-community/datetimepicker`) replaces the old platform-specific `DatePickerIOS`/`DatePickerAndroid`, with a single `mode` prop switching between date, time, and combined selection — though the show/hide interaction still differs by platform.
