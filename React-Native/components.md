# Components

## ScrollView

`ScrollView` is a core component that provides scrolling behavior for overflowing content. It can scroll vertically (default) or horizontally when `horizontal` prop is set. Use it when you have a small amount of scrollable content; for large lists prefer `FlatList` or `SectionList`.

### Basic Usage

```js
import React from 'react';
import { ScrollView, Text, View, StyleSheet } from 'react-native';

export default function App() {
  return (
    <ScrollView style={styles.container}>
      {[...Array(20).keys()].map(i => (
        <View key={i} style={styles.box}>
          <Text>Item {i + 1}</Text>
        </View>
      ))}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1 },
  box: { height: 100, justifyContent: 'center', alignItems: 'center', borderBottomWidth: 1 }
});
```

### Horizontal Scrolling

```js
<ScrollView horizontal>
  <View style={{width: 100, height: 100, backgroundColor: 'red'}} />
  <View style={{width: 100, height: 100, backgroundColor: 'blue'}} />
  <View style={{width: 100, height: 100, backgroundColor: 'green'}} />
</ScrollView>
```

### Useful Props

- `contentContainerStyle`: style for inner container (e.g. padding).
- `showsVerticalScrollIndicator` / `showsHorizontalScrollIndicator`: toggle indicator visibility.
- `onScroll` / `onMomentumScrollEnd`: scroll event callbacks.
- `refreshControl`: pull-to-refresh component for iOS/Android.

> **Tip:** Nesting a `ScrollView` inside another scrollable container can cause performance issues; prefer a single scrollable parent.

`ScrollView` is handy for simple scrollable screens, forms, or galleries where items are relatively few and static.
