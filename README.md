# World's Tallest Mountains (Vue SPA, COMP.7214 Assignment 1)

A full-screen single page application built with Vue 3 and Vite. It shows the
five tallest mountains in the world as a swipeable card deck with a top nav bar.

## Add the photos
Put five images in the `public` folder named exactly:
  everest.jpg, K2.avif, kangchenjunga.avif, lhotse.jpeg, makalu.jpg
Use free-licensed images (Unsplash, Pexels, or Wikimedia Commons) and note the
source of each for your references. If a photo is missing the app falls back to
a drawn SVG mountain, so it still runs.

## How to run
1. Install Node.js (version 18 or newer).
2. In this folder, run:
   npm install
   npm run dev
3. Open the local URL shown in the terminal (usually http://localhost:5173).

## What it demonstrates
- A Vue 3 single-file component using the Composition API (script setup).
- Reactive state with ref and the onMounted / onUnmounted lifecycle hooks.
- Rendering a list from a local data source with v-for.
- Handling pointer and keyboard input to drive a card-deck carousel.
