# Plan: Creating a SketchBoard Component Using drawing-board Ideas

## 1. Project Setup

- Use the [react-native-expo-component-template](https://github.com/zestic/react-native-expo-component-template) as the base.
- Place your new component in `src/components/SketchBoard/`.
- Use TypeScript for type safety and maintainability.

## 2. Core Features to Implement

- Freehand drawing using Skia (`@shopify/react-native-skia`).
- Color and stroke selection.
- Undo/redo functionality.
- Export to SVG or image.

## 3. Steps to Build

### A. Scaffold the Component

- Create `SketchBoard.tsx` in `src/components/SketchBoard/`.
- Define props for:
  - `initialPaths?`, `onChange?`, `strokeColors?`, `strokeWidths?`, etc.

### B. Bring in Drawing Logic

- Adapt the drawing logic from `drawing-board/src/drawing/index.tsx`:
  - Use Skia's `Canvas`, `Path`, and touch handlers.
  - Refactor to use local state or props instead of Zustand.
- Example (simplified):

```tsx
const SketchBoard = ({ strokeColor = 'black', strokeWidth = 2, ...props }) => {
  // Local state for paths
  // Touch handlers for drawing
  // Render Skia Canvas and paths
};
```

### C. Toolbar (Color & Stroke)

- Bring in and adapt `Toolbar`, `Color`, and `Stroke` components.
- Make them stateless and controlled via props/callbacks.

### D. Undo/Redo & History

- Refactor `history.tsx` logic to use local state or context.
- Expose undo/redo as methods or via props.

### E. Export/Import

- Use/adapt `makeSvgFromPaths` from `utils.tsx` for SVG export.
- Optionally, add image export using Skia snapshot APIs.

### F. Storybook & Tests

- Add stories in `SketchBoard.stories.tsx`.
- Add tests in `__tests__/SketchBoard.test.tsx`.

## 4. Areas of Improvement from drawing-board

### State Management

- **Abstract state management** so the component can use local state, context, or an external state engine (e.g., Redux, Zustand, or AsyncStorage for persistence).
- **How to achieve this:**
  - Define a state interface for the drawing state and actions.
  - Allow the component to accept a state provider or hooks as props, or use a context provider.
  - Default to internal React state, but allow injection of custom state engines.

#### Example State Interface

```ts
export interface ISketchBoardState {
  paths: PathType[];
  setPaths: (paths: PathType[]) => void;
  color: string;
  setColor: (color: string) => void;
  strokeWidth: number;
  setStrokeWidth: (width: number) => void;
  // Optional: Undo/Redo support
  undo?: () => void;
  redo?: () => void;
  canUndo?: boolean;
  canRedo?: boolean;
  // Optional: Eraser support
  erasePathAtPoint?: (point: { x: number; y: number }) => void;
  isErasing?: boolean;
  setIsErasing?: (erasing: boolean) => void;
}
```

#### Usage Example

```tsx
// Default: use internal state
const [paths, setPaths] = useState<PathType[]>([]);
const [color, setColor] = useState('black');
const [strokeWidth, setStrokeWidth] = useState(2);
const [isErasing, setIsErasing] = useState(false);

// Or: accept state from props/context
const sketchState = props.stateProvider || {
  paths,
  setPaths,
  color,
  setColor,
  strokeWidth,
  setStrokeWidth,
  undo: undefined,
  redo: undefined,
  canUndo: false,
  canRedo: false,
  erasePathAtPoint: undefined,
  isErasing,
  setIsErasing,
};

// Use sketchState in your component
```

- **Componentization:**
  - Decouple UI (toolbar, color, stroke) from logic for easier reuse.
- **API Design:**
  - Expose a clean, minimal API for consumers (props, callbacks, methods).
- **Performance:**
  - Use Skia efficiently; batch updates, avoid unnecessary re-renders.
- **Type Safety:**
  - Add/expand TypeScript types for all props and internal logic.
- **Testing:**
  - Add unit and integration tests (missing in original).
- **Docs:**
  - Provide clear usage examples and prop documentation.

## 5. Example: SketchBoard Component Skeleton

```tsx
import React, { useState } from 'react';
import {
  Canvas,
  Path,
  Skia,
  useDrawCallback,
} from '@shopify/react-native-skia';

export const SketchBoard = ({
  strokeColor = 'black',
  strokeWidth = 2,
  ...props
}) => {
  const [paths, setPaths] = useState([]);
  // ...drawing logic here
  return (
    <Canvas style={{ flex: 1 }}>
      {paths.map((p, i) => (
        <Path key={i} path={p.path} paint={p.paint} />
      ))}
    </Canvas>
  );
};
```

## 6. Next Steps

- Scaffold the component using the template.
- Incrementally migrate and refactor code from `drawing-board`.
- Test and document each feature as you go.

## Incremental plan:

- MVP: Basic Drawing
- State Management Abstraction
- Erase Feature (using erasePathAtPoint and eraser state)
- Undo/Redo (expose via interface)
- Exporting
- Persistence

---

This plan will help you create a modern, reusable SketchBoard component with best practices and improved maintainability.

## 7. Theming Support

### Incremental Adoption: Using Defaults First

You can start with the current hardcoded color values as your defaults. As you add theme support, refactor your styles to use theme variables, but always fall back to the existing defaults if a theme is not provided. This allows you to incrementally migrate to full theme support without breaking existing behavior.

**Example:**

```tsx
const DEFAULT_BG = '#f0f0f0';
const DEFAULT_CANVAS = '#ffffff';

const SketchBoard = ({ theme, ...props }) => {
  const backgroundColor = theme?.backgroundColor || DEFAULT_BG;
  const canvasColor = theme?.canvasColor || DEFAULT_CANVAS;
  // ...use these in your styles
};
```

This approach lets you safely roll out theming in stages and ensures backward compatibility until full theme support is in place.

### Overview

The original `drawing-board` project uses hardcoded colors and does not support theming or light/dark mode. For a reusable SketchBoard component, supporting themes (light, dark, or custom) is highly recommended.

### Plan for Theming

- Refactor all hardcoded colors to use variables, props, or a theme object.
- Accept a `theme` prop or use a React context/provider for theme values.
- Optionally, use React Native's `useColorScheme` to auto-detect system theme and apply styles.
- Document theme structure and provide sensible defaults.

### Example Theme Interface

```ts
export interface SketchBoardTheme {
  backgroundColor: string;
  canvasColor: string;
  toolbarColor: string;
  borderColor: string;
  // Add more as needed (button, icon, etc.)
}
```

### Example Usage in Component

```tsx
const defaultLightTheme: SketchBoardTheme = {
  backgroundColor: '#f0f0f0',
  canvasColor: '#ffffff',
  toolbarColor: '#ffffff',
  borderColor: '#f0f0f0',
};

const defaultDarkTheme: SketchBoardTheme = {
  backgroundColor: '#181818',
  canvasColor: '#222222',
  toolbarColor: '#222222',
  borderColor: '#333333',
};

const SketchBoard = ({ theme, ...props }) => {
  const colorScheme = useColorScheme?.() || 'light';
  const appliedTheme =
    theme || (colorScheme === 'dark' ? defaultDarkTheme : defaultLightTheme);

  return (
    <View style={{ flex: 1, backgroundColor: appliedTheme.backgroundColor }}>
      <View
        style={{
          backgroundColor: appliedTheme.canvasColor,
          borderColor: appliedTheme.borderColor,
        }}
      >
        {/* ... */}
      </View>
      <Toolbar style={{ backgroundColor: appliedTheme.toolbarColor }} />
    </View>
  );
};
```

### Recommendations

- Replace all color literals in styles with values from the theme.
- Document the theme prop and provide both light and dark defaults.
- Allow users to override any theme value for custom branding.

# Plan: Creating a SketchBoard Component Using drawing-board Ideas

## 1. Project Setup

- Use the [react-native-expo-component-template](https://github.com/zestic/react-native-expo-component-template) as the base.
- Place your new component in `src/components/SketchBoard/`.
- Use TypeScript for type safety and maintainability.

## 2. Core Features to Implement

- Freehand drawing using Skia (`@shopify/react-native-skia`).
- Color and stroke selection.
- Undo/redo functionality.
- Export to SVG or image.

## 3. Steps to Build

### A. Scaffold the Component

- Create `SketchBoard.tsx` in `src/components/SketchBoard/`.
- Define props for:
  - `initialPaths?`, `onChange?`, `strokeColors?`, `strokeWidths?`, etc.

### B. Bring in Drawing Logic

- Adapt the drawing logic from `drawing-board/src/drawing/index.tsx`:
  - Use Skia's `Canvas`, `Path`, and touch handlers.
  - Refactor to use local state or props instead of Zustand.
- Example (simplified):

```tsx
const SketchBoard = ({ strokeColor = 'black', strokeWidth = 2, ...props }) => {
  // Local state for paths
  // Touch handlers for drawing
  // Render Skia Canvas and paths
};
```

### C. Toolbar (Color & Stroke)

- Bring in and adapt `Toolbar`, `Color`, and `Stroke` components.
- Make them stateless and controlled via props/callbacks.

### D. Undo/Redo & History

- Refactor `history.tsx` logic to use local state or context.
- Expose undo/redo as methods or via props.

### E. Export/Import

- Use/adapt `makeSvgFromPaths` from `utils.tsx` for SVG export.
- Optionally, add image export using Skia snapshot APIs.

### F. Storybook & Tests

- Add stories in `SketchBoard.stories.tsx`.
- Add tests in `__tests__/SketchBoard.test.tsx`.

## 4. Areas of Improvement from drawing-board

### State Management

- **Abstract state management** so the component can use local state, context, or an external state engine (e.g., Redux, Zustand, or AsyncStorage for persistence).
- **How to achieve this:**
  - Define a state interface for the drawing state and actions.
  - Allow the component to accept a state provider or hooks as props, or use a context provider.
  - Default to internal React state, but allow injection of custom state engines.

#### Example State Interface

```ts
export interface ISketchBoardState {
  paths: PathType[];
  setPaths: (paths: PathType[]) => void;
  color: string;
  setColor: (color: string) => void;
  strokeWidth: number;
  setStrokeWidth: (width: number) => void;
  // Optional: Undo/Redo support
  undo?: () => void;
  redo?: () => void;
  canUndo?: boolean;
  canRedo?: boolean;
  // Optional: Eraser support
  erasePathAtPoint?: (point: { x: number; y: number }) => void;
  isErasing?: boolean;
  setIsErasing?: (erasing: boolean) => void;
}
```

#### Usage Example

```tsx
// Default: use internal state
const [paths, setPaths] = useState<PathType[]>([]);
const [color, setColor] = useState('black');
const [strokeWidth, setStrokeWidth] = useState(2);
const [isErasing, setIsErasing] = useState(false);

// Or: accept state from props/context
const sketchState = props.stateProvider || {
  paths,
  setPaths,
  color,
  setColor,
  strokeWidth,
  setStrokeWidth,
  undo: undefined,
  redo: undefined,
  canUndo: false,
  canRedo: false,
  erasePathAtPoint: undefined,
  isErasing,
  setIsErasing,
};

// Use sketchState in your component
```

- **Componentization:**
  - Decouple UI (toolbar, color, stroke) from logic for easier reuse.
- **API Design:**
  - Expose a clean, minimal API for consumers (props, callbacks, methods).
- **Performance:**
  - Use Skia efficiently; batch updates, avoid unnecessary re-renders.
- **Type Safety:**
  - Add/expand TypeScript types for all props and internal logic.
- **Testing:**
  - Add unit and integration tests (missing in original).
- **Docs:**
  - Provide clear usage examples and prop documentation.

## 5. Example: SketchBoard Component Skeleton

```tsx
import React, { useState } from 'react';
import {
  Canvas,
  Path,
  Skia,
  useDrawCallback,
} from '@shopify/react-native-skia';

export const SketchBoard = ({
  strokeColor = 'black',
  strokeWidth = 2,
  ...props
}) => {
  const [paths, setPaths] = useState([]);
  // ...drawing logic here
  return (
    <Canvas style={{ flex: 1 }}>
      {paths.map((p, i) => (
        <Path key={i} path={p.path} paint={p.paint} />
      ))}
    </Canvas>
  );
};
```

## 6. Next Steps

- Scaffold the component using the template.
- Incrementally migrate and refactor code from `drawing-board`.
- Test and document each feature as you go.

## Incremental plan:

- MVP: Basic Drawing
- State Management Abstraction
- Erase Feature (using erasePathAtPoint and eraser state)
- Undo/Redo (expose via interface)
- Exporting
- Persistence

---

This plan will help you create a modern, reusable SketchBoard component with best practices and improved maintainability.
