<template>
  <component
    :is="tag"
    v-if="!!$slots.default"
    :style="setStyle"
    :class="{folding_open:active, folding_ing: isAnimation}"
    :aria-hidden="active?'false':'true'"
  >
    <div class="inner">
      <slot />
    </div>
  </component>
</template>

<script>
import uiEventBus from '../uiEventBus.vue';

export default {
  name: 'kbFoldingPanel',
  props: {
    active: Boolean,
    tag: { type: String, default: 'div' },
    duration: { type: Number, default: 300 },
    endScroll: { type: Boolean, default: true },
    scrolling: { type: Boolean, default: null },
  },
  data() {
    return {
      setStyle: null,
      isScroll: true,
      isAnimation: false,
    };
  },
  watch: {
    endScroll() {
      this.isScroll = this.endScroll;
    },
    active() {
      this.folding();
    },
    scrolling() {
      if (this.scrolling) this.foldingCallback();
    },
  },
  created() {
    if (!this.active) {
      this.setStyle = {
        display: 'none',
      };
    }
  },
  methods: {
    folding() {
      const intervalTime = 20;
      if (this.active) {
        this.isAnimation = true;
        this.$slideDown(this.$el, this.duration, () => {
          if (this.isScroll) this.foldingCallback();
          this.isAnimation = false;
        });
      } else {
        this.isAnimation = true;
        this.$slideUp(this.$el, this.duration, () => {
          this.isAnimation = false;
        });
      }

      if (this.$el.closest('.main_swiper') !== null) {
        let mainUpdateTime = 0;
        const mainUpdate = setInterval(() => {
          mainUpdateTime += intervalTime;
          uiEventBus.$emit('main-update');
          if (mainUpdateTime >= this.duration)clearInterval(mainUpdate);
        }, intervalTime);
      }
    },
    foldingCallback() {
      const el = this.$el.parentNode;
      let wrap = el.closest('.scl__body');
      if (wrap === null)wrap = window.document.scrollingElement || window.document.body || window.document.documentElement;
      const wrapH = wrap.offsetHeight === undefined ? wrap.innerHeight : wrap.offsetHeight;
      const wrapSclTop = wrap.scrollTop;
      const wrapEnd = wrapSclTop + wrapH;
      const elTop = this.$getOffset(el).top;
      const elH = el.offsetHeight;
      const elEnd = elTop + elH;
      let topGap = 0;
      const header = wrap.querySelector('.scl__head');
      if (header != null)topGap += (header.offsetHeight);
      // const fixedTab = wrap.querySelector('.tab_wrap.fixed');
      // if (fixedTab != null)topGap += (fixedTab.querySelector('.tabmenu').offsetHeight);
      const tabAll = wrap.querySelectorAll('.tab__fixed');
      tabAll.forEach((item) => {
        const height = item.offsetHeight;
        topGap += height;
      });
      const wrapStart = wrapSclTop + topGap;
      let bottomGap = 0;
      const space = wrap.querySelector('.fixed_space');
      if (space != null)bottomGap += (space.offsetHeight);
      const startY = elTop - topGap;
      const endY = elEnd - wrapH + bottomGap + 10;
      const sclMove = Math.min(startY, endY);
      if (elTop < wrapStart) {
        this.$scrollTo(wrap, { top: startY }, 300);
      } else if (wrapEnd < elEnd) {
        this.$scrollTo(wrap, { top: sclMove }, 300);
      }
    },
  },
};
</script>
