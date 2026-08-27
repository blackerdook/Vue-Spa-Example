<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

// Local data source: the five tallest mountains in the world.
const mountains = [
  {
    rank: 1, name: 'Mount Everest', height_m: 8849, height_ft: 29032,
    range: 'Mahalangur Himalaya', country: 'Nepal / China', firstAscent: 1953,
    desc: 'The highest point on Earth, measured from sea level. First summited by Edmund Hillary and Tenzing Norgay in 1953.',
    image: '/everest.jpg',
    sky: 'linear-gradient(180deg, #1e3a5f 0%, #4a7ba6 55%, #cfe3f0 100%)',
    back: '#6b93b8', mid: '#3f6d92', front: '#274b68',
  },
  {
    rank: 2, name: 'K2', height_m: 8611, height_ft: 28251,
    range: 'Karakoram', country: 'Pakistan / China', firstAscent: 1954,
    desc: 'Known as the Savage Mountain for its difficulty and danger. The second highest peak and one of the hardest to climb.',
    image: '/K2.avif',
    sky: 'linear-gradient(180deg, #3a2f4f 0%, #7a5f8f 55%, #e6dcef 100%)',
    back: '#9a7fb0', mid: '#6d5088', front: '#43315a',
  },
  {
    rank: 3, name: 'Kangchenjunga', height_m: 8586, height_ft: 28169,
    range: 'Himalayas', country: 'Nepal / India', firstAscent: 1955,
    desc: 'The third highest mountain in the world, whose name means the Five Treasures of Snow. Sacred to the people of Sikkim.',
    image: '/kangchenjunga.avif',
    sky: 'linear-gradient(180deg, #1f4a3f 0%, #3f8f74 55%, #d6f0e6 100%)',
    back: '#5fb094', mid: '#2f8068', front: '#1c5545',
  },
  {
    rank: 4, name: 'Lhotse', height_m: 8516, height_ft: 27940,
    range: 'Mahalangur Himalaya', country: 'Nepal / China', firstAscent: 1956,
    desc: 'Connected to Everest by the South Col. Its steep South Face is one of the most demanding walls in the Himalayas.',
    image: '/lhotse.jpeg',
    sky: 'linear-gradient(180deg, #5a3a2f 0%, #a6704a 55%, #f0e0d0 100%)',
    back: '#c9926a', mid: '#9a6440', front: '#653f28',
  },
  {
    rank: 5, name: 'Makalu', height_m: 8485, height_ft: 27838,
    range: 'Mahalangur Himalaya', country: 'Nepal / China', firstAscent: 1955,
    desc: 'An isolated peak shaped like a four-sided pyramid. Its sharp ridges and steep faces make it a serious mountaineering challenge.',
    image: '/makalu.jpg',
    sky: 'linear-gradient(180deg, #2f3a4f 0%, #566d8f 55%, #dfe6f0 100%)',
    back: '#8296b3', mid: '#556d92', front: '#333f5a',
  },
];

const index = ref(0);
const drag = ref(0);
const dragging = ref(false);
let startX = 0;

const count = mountains.length;
function goTo(i) {
  index.value = Math.max(0, Math.min(count - 1, i));
}

// Arrow-key navigation.
function onKey(e) {
  if (e.key === 'ArrowRight') goTo(index.value + 1);
  if (e.key === 'ArrowLeft') goTo(index.value - 1);
}
onMounted(() => window.addEventListener('keydown', onKey));
onUnmounted(() => window.removeEventListener('keydown', onKey));

// Pointer handlers give swipe on touch and drag on desktop.
function onDown(e) {
  if (e.target.closest('button')) return; // let arrows/dots/nav work
  dragging.value = true;
  startX = e.clientX;
  e.currentTarget.setPointerCapture(e.pointerId);
}
function onMove(e) {
  if (!dragging.value) return;
  drag.value = e.clientX - startX;
}
function onUp() {
  if (!dragging.value) return;
  dragging.value = false;
  if (drag.value < -90) goTo(index.value + 1);
  else if (drag.value > 90) goTo(index.value - 1);
  drag.value = 0;
}


function cardStyle(i) {
  const offset = i - index.value;
  const ease = 'transform 0.55s cubic-bezier(0.22, 1, 0.36, 1), opacity 0.55s ease';

  if (dragging.value && offset === 0) {
    return { transition: 'none', transform: `translateX(${drag.value}px) rotate(${drag.value * 0.02}deg)`, zIndex: 120, opacity: 1 };
  }
  if (offset === 0) return { transition: ease, transform: 'translateX(0px) scale(1) rotate(0deg)', zIndex: 100, opacity: 1 };
  if (offset === 1) return { transition: ease, transform: 'translateX(34px) scale(0.93) rotate(2deg)', zIndex: 99, opacity: 1 };
  if (offset === 2) return { transition: ease, transform: 'translateX(64px) scale(0.86) rotate(4deg)', zIndex: 98, opacity: 1 };
  if (offset > 2)   return { transition: ease, transform: 'translateX(90px) scale(0.82) rotate(5deg)', zIndex: 97, opacity: 0 };
  return { transition: ease, transform: `translateX(-130%) rotate(${offset * 4}deg) scale(0.96)`, zIndex: 200, opacity: 0 };
}

function hideImg(e) {
  e.target.style.display = 'none';
}
</script>

<template>
  <div class="app">
    <nav class="navbar">
      <span class="brand">▲ SUMMITS</span>
      <ul class="navlinks">
        <li v-for="(m, i) in mountains" :key="m.rank">
          <button :class="['navlink', { active: i === index }]" @click="goTo(i)">{{ m.name }}</button>
        </li>
      </ul>
    </nav>

    <div
      class="carousel"
      @pointerdown="onDown"
      @pointermove="onMove"
      @pointerup="onUp"
      @pointercancel="onUp"
    >
      <div class="deck">
        <section
          v-for="(m, i) in mountains"
          :key="m.rank"
          :class="['slide', { active: i === index }]"
          :style="{ ...cardStyle(i), background: m.sky }"
        >
          <svg class="scene" viewBox="0 0 1200 800" preserveAspectRatio="xMidYMax slice" aria-hidden="true">
            <polygon :fill="m.back" opacity="0.85" points="0,800 0,470 210,300 400,430 560,290 760,440 980,300 1200,470 1200,800" />
            <polygon :fill="m.mid" points="0,800 0,560 260,360 470,540 680,340 900,540 1200,360 1200,800" />
            <polygon :fill="m.front" points="0,800 360,470 560,620 820,430 1060,600 1200,520 1200,800" />
            <polygon fill="#ffffff" opacity="0.9" points="820,430 880,485 800,500 760,478" />
            <polygon fill="#ffffff" opacity="0.9" points="560,620 610,660 520,672 495,648" />
            <polygon fill="#ffffff" opacity="0.85" points="680,340 726,382 636,395 612,372" />
          </svg>
          <img class="photo" :src="m.image" :alt="m.name" @error="hideImg" />
          <div class="shade"></div>
          <div class="overlay">
            <span class="rank">#{{ m.rank }} tallest</span>
            <h1 class="mname">{{ m.name }}</h1>
            <div class="stats">
              <span>{{ m.height_m.toLocaleString() }} m</span>
              <span>{{ m.height_ft.toLocaleString() }} ft</span>
              <span>{{ m.range }}</span>
              <span>{{ m.country }}</span>
              <span>First ascent {{ m.firstAscent }}</span>
            </div>
            <p class="desc">{{ m.desc }}</p>
          </div>
        </section>
      </div>

      <button class="arrow left" @click="goTo(index - 1)" :disabled="index === 0" aria-label="Previous">‹</button>
      <button class="arrow right" @click="goTo(index + 1)" :disabled="index === count - 1" aria-label="Next">›</button>

      <div class="dots">
        <button
          v-for="(m, i) in mountains"
          :key="m.rank"
          :class="['dot', { active: i === index }]"
          @click="goTo(i)"
          :aria-label="'Go to ' + m.name"
        ></button>
      </div>

      <span class="hint">swipe or use ‹ ›</span>
    </div>
  </div>
</template>
