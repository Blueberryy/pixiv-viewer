<template>
  <div class="image-layout" :class="wfClass">
    <masonry v-if="isMasonry" v-bind="masonryProps">
      <slot></slot>
    </masonry>
    <div v-else class="justified-container">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import store from '@/store'

const { wfType, isImageFitScreen } = store.state.appSetting
const masonryProps = {
  gutter: '10px',
  cols: isImageFitScreen
    ? {
        300: 1,
        600: 2,
        900: 3,
        1200: 4,
        1600: 5,
        1920: 6,
        2400: 7,
        2700: 8,
        3000: 9,
        default: 6,
      }
    : {
        300: 1,
        600: 2,
        1200: 3,
        default: 4,
      },
}

export default {
  props: {
    layout: {
      type: String,
      required: false,
    },
  },
  data() {
    return {
      wfType,
      masonryProps,
    }
  },
  computed: {
    isMasonry() {
      if (['Masonry', 'Grid'].includes(this.layout)) return true
      if (this.layout == 'Justified') return false
      return ['Masonry', 'Grid'].includes(this.wfType)
    },
    wfClass() {
      return {
        'wf-grid': this.layout == 'Grid' || this.wfType == 'Grid',
      }
    },
  },
}
</script>

<style>
.wf-grid .image-card-wrapper {
  padding-bottom: 100% !important;
}

.wf-grid .outer-meta .author {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box !important;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
}

.justified-container {
  display: flex !important;
  flex-wrap: wrap;
  gap: 10px;
}

.justified-container::after {
  content: '';
  flex-grow: 999999999;
  min-width: 200px;
  height: 0;
}

.justified-container .image-card {
  --jstf-w: 320;
  --jstf-w-px: 320PX;
  flex-grow: calc(var(--w) * var(--jstf-w) / var(--h));
  width: calc(var(--w) * var(--jstf-w-px) / var(--h));
}

.justified-container .image-card:not(.isOuterMeta) {
  margin-bottom: 0 !important;
}

@media screen and (max-width: 500px) {
  .justified-container .image-card {
    --jstf-w: 240;
    --jstf-w-px: 240PX;
  }
}

.justified-container .image-card-wrapper {
  position: relative !important;
  aspect-ratio: var(--w) / var(--h);
  margin-bottom: 0 !important;
  padding-bottom: 0 !important;
}

.justified-container .image-card-wrapper::before {
  content: '';
  display: block;
  padding-bottom: calc(var(--h) / var(--w) * 100%) !important;
}

.flexbin {
  display: flex !important;
  overflow: hidden;
  flex-wrap: wrap;
  margin: 2.5px;
  transition: 0.2s;
}

.flexbin:after {
  content: '';
  flex-grow: 999999999;
  min-width: 15vw;
  height: 0;
}

.flexbin .image-card {
  position: relative;
  display: block !important;
  height: 15vw !important;
  margin: 2.5px;
  padding-bottom: 0 !important;
  flex-grow: 1;
  transition: 0.2s;
}

.flexbin .image[lazy="loading"] {
  top: unset !important;
  left: unset !important;
  transform: scale(0.5) !important;
}

.flexbin .image {
  position: initial !important;
  width: auto !important;
  height: 15vw !important;
  object-fit: cover;
  max-width: 100%;
  min-width: 100%;
  vertical-align: bottom;
  transition: 0.2s;
}

@media (min-width: 768px) {
  .flexbin .meta .title {
    font-size: 23px !important;
  }

  .flexbin .meta .author {
    font-size: 18px !important;
  }
}

@media (max-width: 1280px) {
  .flexbin:after {
    min-width: 20vw !important;
  }

  .flexbin .image-card {
    height: 20vw !important;
  }

  .flexbin .image {
    height: 20vw !important;
  }
}

@media (max-width: 980px) {
  .flexbin:after {
    min-width: 30vw !important;
  }

  .flexbin .image-card {
    height: 30vw !important;
  }

  .flexbin .image {
    height: 30vw !important;
  }
}

@media (max-width: 768px) {
  .flexbin:after {
    min-width: 40vw !important;
  }

  .flexbin .image-card {
    height: 40vw !important;
  }

  .flexbin .image {
    height: 40vw !important;
  }
}

@media (max-width: 570px) {
  .flexbin:after {
    min-width: 60vw !important;
  }

  .flexbin .image-card {
    height: 60vw !important;
  }

  .flexbin .image {
    height: 60vw !important;
  }
}
</style>
