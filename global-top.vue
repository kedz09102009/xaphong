<template>
  <div v-if="!isIntroSlide" class="shimmer-layer" aria-hidden="true">
    <div class="shimmer shimmer-1"></div>
    <div class="shimmer shimmer-2"></div>
    <div class="shimmer shimmer-3"></div>
  </div>

  <div v-if="!isIntroSlide" class="mist-layer" aria-hidden="true">
    <div class="cloud cloud-1">
      <div class="cloud-core"></div>
    </div>
    <div class="cloud cloud-2">
      <div class="cloud-core"></div>
    </div>
    <div class="cloud cloud-3">
      <div class="cloud-core"></div>
    </div>
  </div>

  <div v-if="!isIntroSlide" class="bubble-layer" aria-hidden="true">
    <span
      v-for="bubble in bubbles"
      :key="bubble.id"
      class="soap-bubble"
      :style="{
        '--left': bubble.left + '%',
        '--size': bubble.size + 'px',
        '--duration': bubble.duration + 's',
        '--delay': bubble.delay + 's'
      }"
    ></span>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useSlideContext } from '@slidev/client'

const { $nav } = useSlideContext()

const isIntroSlide = computed(() => $nav.currentPage === 1)

const bubbles = Array.from({ length: 12 }, (_, i) => ({
  id: i,
  left: (i * 37 + 13) % 100,
  size: 35 + (i * 23) % 35,
  duration: 9 + (i * 7) % 12,
  delay: -(i * 2.3) % 18
}))
</script>

<style>
.bubble-layer {
  position: fixed;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
  z-index: 10;
}

.soap-bubble {
  position: absolute;
  left: var(--left);
  bottom: -90px;

  width: var(--size);
  height: var(--size);

  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.95);

  background:
  radial-gradient(
    circle at 28% 25%,
    rgba(255, 255, 255, 0.85) 0%,
    rgba(255, 255, 255, 0.3) 8%,
    rgba(150, 220, 255, 0.16) 25%,
    rgba(255, 150, 220, 0.12) 48%,
    rgba(150, 200, 255, 0.1) 70%,
    rgba(255, 255, 255, 0.04) 100%
  );

box-shadow:
    inset 0 0 12px rgba(255, 255, 255, 0.65),
    inset -8px -8px 15px rgba(100, 180, 255, 0.16),
    inset 5px 5px 12px rgba(255, 255, 255, 0.65),
    0 0 12px rgba(160, 220, 255, 0.2);
  animation:
    bubble-float var(--duration) linear var(--delay) infinite;

  will-change: transform, opacity;
}

@keyframes bubble-float {
  0% {
    transform: translate3d(0, 0, 0) scale(0.8);
    opacity: 0;
  }

  10% {
    opacity: 0.95;
  }

  50% {
    transform: translate3d(35px, -55vh, 0) scale(1.05);
    opacity: 0.9;
  }

  90% {
    opacity: 0.85;
  }

  100% {
    transform: translate3d(-20px, -110vh, 0) scale(1.2);
    opacity: 0;
  }
}
/* =================================
   NATURAL FLOATING BLUE CLOUDS
================================= */

.mist-layer {
  position: fixed;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
  z-index: 20;
  isolation: isolate;
}

.cloud {
  position: absolute;
  width: 420px;
  height: 180px;
  opacity: 0.2;
  filter: blur(22px);
  will-change: transform;

  animation: cloud-flow 45s linear infinite !important;
}

/* Các khối tạo hình đám mây */
.cloud::before,
.cloud::after {
  content: "";
  position: absolute;
  border-radius: 50%;
  background: rgba(75, 165, 230, 0.48);
}

.cloud::before {
  width: 58%;
  height: 115%;
  left: 12%;
  top: -30%;
}

.cloud::after {
  width: 65%;
  height: 100%;
  right: 8%;
  top: -15%;
}

.cloud-core {
  position: absolute;
  inset: 15% 5% 0;
  border-radius: 50%;
  background:
    radial-gradient(
      ellipse,
      rgba(65, 155, 225, 0.48) 0%,
      rgba(90, 180, 240, 0.28) 45%,
      transparent 75%
    );
}

/* Vị trí từng cụm mây */
.cloud-1 {
  bottom: 4%;
  left: 0;
  animation: cloud-flow 55s linear infinite;
  animation-delay: 0s;
  scale: 0.75;
}

.cloud-2 {
  bottom: 13%;
  left: 0;
  animation: cloud-flow 70s linear infinite;
  animation-delay: -24s;
  scale: 0.55;
}

.cloud-3 {
  bottom: 1%;
  left: 0;
  animation: cloud-flow 62s linear infinite;
  animation-delay: -43s;
  scale: 0.9;
}


/* Chuyển động */
@keyframes cloud-flow {
  from {
    transform: translateX(-550px);
  }

  to {
    transform: translateX(calc(100vw + 550px));
  }
}

@media (prefers-reduced-motion: reduce) {
  .cloud {
    animation: none;
  }
}

/* ===== IRIDESCENT SHIMMER V2 ===== */

.shimmer-layer {
  position: fixed;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
  z-index: 30;
}

.shimmer {
  position: absolute;
  left: -650px;

  width: 600px;
  height: 24px;

  border-radius: 50%;

  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(90, 220, 255, 0.12) 15%,
    rgba(120, 210, 255, 0.55) 30%,
    rgba(255, 255, 255, 0.8) 43%,
    rgba(255, 155, 220, 0.5) 57%,
    rgba(170, 160, 255, 0.4) 72%,
    transparent 100%
  );

  filter: blur(9px);
  opacity: 0.7;

  transform: rotate(-18deg);
  animation: shimmer-sweep 32s linear infinite;

  will-change: transform;
}

.shimmer-1 {
  top: 22%;
  animation-delay: 0s;
}

.shimmer-2 {
  top: 52%;
  animation-delay: -11s;
  opacity: 0.5;
}

.shimmer-3 {
  top: 78%;
  animation-delay: -22s;
  opacity: 0.4;
}

@keyframes shimmer-sweep {
  from {
    translate: 0 0;
  }

  to {
    translate: calc(100vw + 1300px) 0;
  }
}
</style>