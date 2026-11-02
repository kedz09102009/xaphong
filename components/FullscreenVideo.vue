<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const props = defineProps({
  src: { type: String, required: true },
  poster: { type: String, default: '' },
})

const videoEl = ref(null)
const ready = ref(false)
const failed = ref(false)
const isPlaying = ref(false)
const ended = ref(false)
const fullscreen = ref(false)
const baseUrl = import.meta.env.BASE_URL || '/'
const videoUrl = computed(() => `${baseUrl}${props.src.replace(/^\/+/, '')}`)
const posterUrl = computed(() => props.poster ? `${baseUrl}${props.poster.replace(/^\/+/, '')}` : undefined)

function onLoadedMetadata() {
  ready.value = true
  failed.value = false
}

function onVideoError() {
  ready.value = false
  failed.value = true
}

function onPlay() {
  isPlaying.value = true
  ended.value = false
}

function onPause() {
  isPlaying.value = false
}

async function startFullscreenPlayback() {
  const video = videoEl.value
  if (!video || !ready.value) return

  ended.value = false
  // Both calls are initiated directly from the user's click so browser
  // fullscreen and autoplay restrictions can be satisfied.
  const fullscreenRequest = video.requestFullscreen
    ? video.requestFullscreen().catch(() => null)
    : Promise.resolve(null)
  const playRequest = video.play()

  const [fullscreenResult, playResult] = await Promise.allSettled([fullscreenRequest, playRequest])
  fullscreen.value = Boolean(document.fullscreenElement)
  if (playResult.status === 'rejected') {
    isPlaying.value = false
  }
  return { fullscreenResult, playResult }
}

async function onEnded() {
  ended.value = true
  isPlaying.value = false
  if (document.fullscreenElement && document.exitFullscreen) {
    try { await document.exitFullscreen() } catch { /* keep the slide usable if exit is blocked */ }
  }
}

function onFullscreenChange() {
  fullscreen.value = Boolean(document.fullscreenElement)
  if (!fullscreen.value && videoEl.value && !ended.value && !videoEl.value.paused) {
    videoEl.value.pause()
  }
}

onMounted(() => document.addEventListener('fullscreenchange', onFullscreenChange))
onBeforeUnmount(() => {
  if (typeof document !== 'undefined') document.removeEventListener('fullscreenchange', onFullscreenChange)
})
</script>

<template>
  <section class="fullscreen-video-card">
    <div class="fullscreen-video-card__topline">
      <span class="film-label">✦ LABORATORY FILM</span>
      <span class="film-meta">VIDEO NGANG · 16:9</span>
    </div>
    <div class="fullscreen-video-card__stage">
      <video
        ref="videoEl"
        class="fullscreen-video-card__video"
        :src="videoUrl"
        :poster="posterUrl"
        :controls="ready"
        playsinline
        preload="metadata"
        @loadedmetadata="onLoadedMetadata"
        @error="onVideoError"
        @play="onPlay"
        @pause="onPause"
        @ended="onEnded"
      />
      <div v-if="!ready" class="fullscreen-video-card__placeholder">
        <div class="film-play-icon">▶</div>
        <strong>{{ failed ? 'Chưa tìm thấy video' : 'HÀNH TRÌNH THỰC HÀNH CỦA NHÓM' }}</strong>
        <span>{{ failed ? 'Kiểm tra lại tên và vị trí tệp video.' : 'Thêm video MP4 của nhóm để kích hoạt trình chiếu.' }}</span>
        <code>public/{{ src }}</code>
      </div>
    </div>
    <div class="fullscreen-video-card__footer">
      <div class="film-caption">
        <strong>HÀNH TRÌNH TẠO RA XÀ PHÒNG</strong>
        <span v-if="ended">Video đã kết thúc · Có thể chuyển sang phần quan sát</span>
        <span v-else-if="isPlaying">Video đang phát · Nhấn Esc để thoát toàn màn hình</span>
        <span v-else>Video sẽ phát toàn màn hình khi nhấn nút bên dưới.</span>
      </div>
      <button class="fullscreen-video-card__button" :disabled="!ready" @click="startFullscreenPlayback">
        <span>▶</span> {{ ended ? 'PHÁT LẠI TOÀN MÀN HÌNH' : 'XEM TOÀN MÀN HÌNH' }}
      </button>
    </div>
  </section>
</template>

<style scoped>
.fullscreen-video-card {
  position: relative;
  display: flex;
  flex-direction: column;
  gap: .7rem;
  padding: .9rem 1rem .8rem;
  border: 2px solid #fff;
  border-radius: 1.25rem;
  background: linear-gradient(145deg, #fffdf8, #edf8ff);
  box-shadow: 0 8px 0 rgba(60,139,184,.13), 0 16px 30px rgba(44,105,146,.12);
}
.fullscreen-video-card__topline,
.fullscreen-video-card__footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}
.film-label {
  display: inline-flex;
  align-items: center;
  padding: .32rem .7rem;
  border-radius: 999px;
  color: #17466d;
  background: #dff2ff;
  font-size: .78rem;
  font-weight: 900;
  letter-spacing: .08em;
}
.film-meta { color: #5c7d94; font-size: .76rem; font-weight: 800; letter-spacing: .06em; }
.fullscreen-video-card__stage {
  position: relative;
  overflow: hidden;
  width: 100%;
  aspect-ratio: 16 / 9;
  border: 2px dashed rgba(52,134,183,.42);
  border-radius: .9rem;
  background: #143b58;
}
.fullscreen-video-card__video {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: contain;
  background: #102e45;
}
.fullscreen-video-card__video:fullscreen {
  width: 100vw;
  height: 100vh;
  max-width: none;
  max-height: none;
  object-fit: contain;
  border: 0;
  border-radius: 0;
  background: #000;
}
.fullscreen-video-card__placeholder {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: .42rem;
  padding: 1rem;
  color: #fff;
  text-align: center;
  pointer-events: none;
  background: radial-gradient(circle at 50% 35%, rgba(123,203,245,.3), transparent 45%);
}
.film-play-icon {
  display: grid;
  place-items: center;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
  color: #17466d;
  background: #fff;
  font-size: 1.2rem;
  box-shadow: 0 5px 15px rgba(0,0,0,.18);
}
.fullscreen-video-card__placeholder strong { font-size: 1.1rem; letter-spacing: .04em; }
.fullscreen-video-card__placeholder span { color: #d9efff; font-size: .88rem; }
.fullscreen-video-card__placeholder code {
  padding: .22rem .48rem;
  border-radius: .35rem;
  color: #17466d;
  background: #fffdf8;
  font-size: .75rem;
}
.film-caption { display: flex; flex-direction: column; gap: .18rem; min-width: 0; }
.film-caption strong { color: #17466d; font-size: .96rem; }
.film-caption span { color: #587991; font-size: .8rem; }
.fullscreen-video-card__button {
  flex: 0 0 auto;
  border: 0;
  border-radius: 999px;
  padding: .68rem .95rem;
  color: #fff;
  background: linear-gradient(135deg,#2589c2,#55b8e8);
  font: inherit;
  font-size: .82rem;
  font-weight: 900;
  letter-spacing: .03em;
  box-shadow: 0 4px 0 rgba(24,102,151,.2);
  cursor: pointer;
}
.fullscreen-video-card__button:disabled { opacity: .48; cursor: not-allowed; }
.fullscreen-video-card__button span { margin-right: .35rem; }
@media (max-width: 760px) {
  .fullscreen-video-card__footer { align-items: stretch; flex-direction: column; }
  .fullscreen-video-card__button { width: 100%; }
}
</style>
