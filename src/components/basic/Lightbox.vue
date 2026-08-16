<script setup lang="ts">
import { onBeforeUnmount, onMounted, watch } from 'vue';
import { useLightbox } from '~/composables/useLightbox';

const { activeImage, close } = useLightbox();

function onKeydown(event: KeyboardEvent) {
  if (event.key === 'Escape') close();
}

watch(activeImage, (value) => {
  if (typeof document === 'undefined') return;
  document.documentElement.style.overflow = value ? 'hidden' : '';
});

onMounted(() => {
  window.addEventListener('keydown', onKeydown);
});

onBeforeUnmount(() => {
  window.removeEventListener('keydown', onKeydown);
  if (typeof document !== 'undefined') {
    document.documentElement.style.overflow = '';
  }
});
</script>

<template>
  <Teleport to="body">
    <Transition name="lightbox-fade">
      <div
        v-if="activeImage"
        class="lightbox-backdrop"
        @click.self="close"
      >
        <button
          type="button"
          class="lightbox-close"
          aria-label="Bild schließen"
          @click="close"
        >
          <span aria-hidden="true">&times;</span>
        </button>

        <img
          :src="activeImage.src"
          :alt="activeImage.alt || ''"
          :width="activeImage.width"
          :height="activeImage.height"
          class="lightbox-image"
        >
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.lightbox-backdrop {
  position: fixed;
  inset: 0;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: clamp(calc(var(--spacing) * 3), 4vw, calc(var(--spacing) * 8));
  background: color-mix(in srgb, var(--color-black) 85%, transparent);
  cursor: zoom-out;
}

.lightbox-image {
  display: block;
  max-width: 100%;
  max-height: 100%;
  width: auto;
  height: auto;
  cursor: default;
  box-shadow: 0 10px 40px color-mix(in srgb, var(--color-black) 50%, transparent);
}

.lightbox-close {
  position: absolute;
  top: calc(var(--spacing) * 3);
  right: calc(var(--spacing) * 3);
  display: flex;
  align-items: center;
  justify-content: center;
  width: calc(var(--spacing) * 8);
  height: calc(var(--spacing) * 8);
  border: none;
  border-radius: 9999px;
  background: color-mix(in srgb, var(--color-white) 15%, transparent);
  color: var(--color-white);
  font-family: var(--font-sans);
  font-size: var(--text-lg);
  line-height: var(--leading-tight);
  cursor: pointer;
  transition: background-color 0.2s ease, color 0.2s ease;
  line-height: 1;
  padding: 0;
}

.lightbox-close span {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: -3.5px;
}

.lightbox-close:hover {
  background: var(--color-primary);
  color: var(--color-white);
}

.lightbox-fade-enter-active,
.lightbox-fade-leave-active {
  transition: opacity 0.2s ease;
}

.lightbox-fade-enter-from,
.lightbox-fade-leave-to {
  opacity: 0;
}
</style>