# SketchBoard React Native Component

**SketchBoard** is a flexible, high-performance drawing component for React Native, inspired by the [drawing-board](https://github.com/ammarahm-ed/drawing-board) project and built with [react-native-skia](https://shopify.github.io/react-native-skia/). It is designed for integration into apps that need sketching, annotation, or freehand drawing features.

---

## Features

- ✏️ **Freehand Drawing** with Skia for smooth, high-FPS rendering
- 🎨 **Color and Stroke Selection** via a customizable toolbar
- ↩️ **Undo/Redo** drawing actions
- � **Export** to SVG or image
- 🧩 **Composable & Stateless**: Use with local state, context, or external state engines
- 🧪 **Storybook & Tests**: Stories and tests included for rapid development

---

## Getting Started

### Installation

```sh
git clone https://github.com/portable-content/react-native-sketch-board.git
cd react-native-sketch-board
yarn install
```

> **Note:** This project uses [Yarn](https://yarnpkg.com/) (Berry) via Corepack. Use only Yarn for all dependency management and scripts.

### Usage

Import and use the SketchBoard component in your app:

```tsx
import { SketchBoard } from '@portable-content/react-native-sketch-board';

export default function MyScreen() {
  return (
    <SketchBoard
      strokeColors={['#000', '#f00', '#0f0', '#00f']}
      strokeWidths={[2, 4, 8]}
      onChange={paths => {
        /* handle path changes */
      }}
    />
  );
}
```

---

## Project Structure

```
src/
├── components/
│   ├── SketchBoard/           # Main SketchBoard component
│   ├── Toolbar/               # Toolbar for color/stroke selection
│   ├── ...                    # Other UI components
│   └── Button/                # Example/test component
└── index.tsx                  # Main library export
```

---

## Development

- **Expo App:**
  ```sh
  yarn start
  ```
- **Storybook:**
  ```sh
  yarn storybook
  yarn storybook:web
  ```
- **Build Library:**
  ```sh
  yarn library:build
  ```
- **Run Tests:**
  ```sh
  yarn test
  ```

---

## Roadmap

- [ ] Freehand drawing with Skia
- [ ] Color and stroke selection
- [ ] Undo/redo support
- [ ] SVG export
- [ ] Image export
- [ ] Pluggable state management (context, Redux, etc.)

---

## Inspiration

This project is inspired by [drawing-board](https://github.com/ammarahm-ed/drawing-board) and aims to provide a reusable, stateless, and easily integrated drawing component for React Native apps.

---

## License

Apache 2.0 Licensed
