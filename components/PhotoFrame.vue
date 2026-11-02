<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  src: { type: String, required: true },
  label: { type: String, default: 'ẢNH THỰC NGHIỆM' },
  title: { type: String, default: 'Ảnh thực tế của nhóm' },
  note: { type: String, default: 'Thay bằng ảnh do nhóm chụp' },
  fit: { type: String, default: 'contain' },
  orientation: { type: String, default: 'landscape' },
})

const loaded = ref(false)
const failed = ref(false)
const baseUrl = import.meta.env.BASE_URL || '/'
const imageUrl = computed(() => `${baseUrl}${props.src.replace(/^\/+/, '')}`)

watch(() => props.src, () => {
  loaded.value = false
  failed.value = false
})

function onLoad() {
  loaded.value = true
  failed.value = false
}

function onError() {
  loaded.value = false
  failed.value = true
}
</script>

<template>
  <figure class="photo-frame" :class="[`photo-frame--${orientation}`, `photo-frame--${fit}`]">
    <div class="photo-frame__tape" aria-hidden="true"></div>
    <div class="photo-frame__image-wrap">
      <img
        v-if="!failed"
        class="photo-frame__image"
        :class="{ 'photo-frame__image--loaded': loaded }"
        :src="imageUrl"
        :alt="title"
        @load="onLoad"
        @error="onError"
      />
      <div v-if="!loaded" class="photo-frame__placeholder">
        <span class="photo-frame__placeholder-icon">✦</span>
        <strong>{{ title }}</strong>
        <small>{{ failed ? 'Chưa tìm thấy tệp ảnh' : note }}</small>
        <code>public/{{ src }}</code>
      </div>
    </div>
    <figcaption>
      <span class="photo-frame__label">{{ label }}</span>
      <span class="photo-frame__caption">{{ title }}</span>
    </figcaption>
  </figure>
</template>

<style scoped>
.photo-frame {
  position: relative;
  display: flex;
  flex-direction: column;
  min-width: 0;
  margin: 0;
  padding: .85rem .85rem .7rem;
  background: #fffdf7;
  border: 2px solid #fff;
  border-radius: 1.1rem;
  box-shadow: 0 8px 0 rgba(60, 139, 184, .13), 0 16px 30px rgba(44, 105, 146, .12);
  transform: rotate(-.35deg);
}
.photo-frame__tape {
  position: absolute;
  z-index: 2;
  top: -.5rem;
  left: 50%;
  width: 5.2rem;
  height: 1.05rem;
  transform: translateX(-50%) rotate(-3deg);
  border-radius: .15rem;
  background: rgba(255, 222, 135, .78);
  box-shadow: 0 2px 4px rgba(50, 98, 125, .08);
}
.photo-frame__image-wrap {
  position: relative;
  min-height: 0;
  flex: 1;
  overflow: hidden;
  display: grid;
  place-items: center;
  border-radius: .68rem;
  border: 1px dashed rgba(50, 133, 184, .32);
  background: repeating-linear-gradient(135deg, #f8fcff 0 16px, #edf7fd 16px 32px);
}
.photo-frame__image {
  display: block;
  width: 100%;
  height: 100%;
  min-height: 12rem;
  object-position: center;
  opacity: 0;
  transition: opacity .35s ease;
}
.photo-frame--contain .photo-frame__image { object-fit: contain; }
.photo-frame--cover .photo-frame__image { object-fit: cover; }
.photo-frame__image--loaded { opacity: 1; }
.photo-frame__placeholder {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: .4rem;
  padding: 1rem;
  color: #285b7c;
  text-align: center;
}
.photo-frame__placeholder-icon {
  display: grid;
  place-items: center;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
  color: #237fb6;
  background: #dff2ff;
  font-size: 1.5rem;
}
.photo-frame__placeholder strong { font-size: 1.16rem; line-height: 1.2; }
.photo-frame__placeholder small { color: #5c7d94; font-size: .88rem; }
.photo-frame__placeholder code {
  color: #47718d;
  background: rgba(255,255,255,.78);
  padding: .2rem .42rem;
  border-radius: .35rem;
  font-size: .7rem;
}
.photo-frame figcaption {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: .7rem;
  padding: .58rem .15rem .05rem;
  color: #17466d;
}
.photo-frame__label {
  flex: 0 0 auto;
  padding: .23rem .55rem;
  border-radius: 999px;
  background: #fff0be;
  color: #775b21;
  font-size: .78rem;
  font-weight: 900;
  letter-spacing: .04em;
}
.photo-frame__caption { font-size: .92rem; font-weight: 800; text-align: right; }
.photo-frame--landscape .photo-frame__image-wrap { aspect-ratio: 16 / 9; }
.photo-frame--portrait .photo-frame__image-wrap { aspect-ratio: 4 / 5; }
.photo-frame--square .photo-frame__image-wrap { aspect-ratio: 1 / 1; }
@media (prefers-reduced-motion: reduce) {
  .photo-frame__image { transition: none; }
}
</style>
