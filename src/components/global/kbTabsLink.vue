<template>
  <div
    class="tab_wrap"
    :class="{fixed:isFixed}"
  >
    <div
      class="tabmenu"
      :class="tabmenuClass"
      :style="fixedStyle"
    >
      <div
        ref="tablist"
        class="tablist"
        :class="{tab__fixed:fixed}"
        role="tablist"
      >
        <div
          class="tab_line"
          aria-hidden="true"
          :style="{width:`${lineWrapWidth}px`,left:`${lineWrapLeft}px`}"
        >
          <i :style="{width:`${lineWidth}px`,left:`${lineLeft}px`}" />
        </div>
        <div
          v-for="(tab, i) in tabs"
          :key="i"
          class="tab"
          :class="{disabled:tab.disabled}"
          role="presentation"
        >
          <a
            v-if="tab.disabled || disabled"
            :class="{'router-link-exact-active':$route.path.indexOf(tab.to) >= 0}"
            role="tab"
            href="#"
            aria-disabled="true"
            @click="$event.preventDefault()"
          >
            <span>{{ tab.text }}</span>
          </a>
          <router-link
            v-else
            :to="tab.to"
            role="tab"
          >
            <span>{{ tab.text }}</span>
          </router-link>
        </div>
        <slot />
      </div>
      <div
        v-if="isScrollable && isScrollableLeft"
        class="tab_blur left"
      />
      <div
        v-if="isScrollable && isScrollableRight"
        class="tab_blur right"
      />
    </div>
    <router-view />
  </div>
</template>

<script>
let uuid = 0;
export default {
  name: 'kbTabsLink',
  props: {
    fixed: { type: Boolean, default: false },
    disabled: { type: Boolean, default: false },
    tabsClass: { type: String, default: null },
    contentClass: { type: String, default: null },
    tabs: { type: Array, default: null },
  },
  data() {
    return {
      isFixed: false,
      lineLeft: 0,
      lineWidth: 0,
      lineWrapWidth: 0,
      lineWrapLeft: 0,
      isAniamte: false,
      lastScrollPosition: 0,
      isScrollable: false,
      isScrollableLeft: false,
      isScrollableRight: false,
      isFixedTopChk: false,
      fixedStyle: null,
    };
  },
  computed: {
    tabmenuClass() {
      return [
        'tabmenu',
        this.tabsClass,
        {
          disabled: this.disabled,
          scrollable: this.isScrollable,
        },
      ];
    },
  },
  watch: {
    $route(to, from) {
      if (to.path !== from.path) {
        let wrap = this.$el.closest('.scl__body');
        let wrapEl = wrap;
        let head = null;
        let wrapSclTop = 0;
        if (wrap === null) {
          wrap = window;
          wrapEl = window.document.scrollingElement || window.document.body || window.document.documentElement;
          wrapSclTop = window.scrollY;
          head = document.querySelector('.scl__head');
        } else {
          wrapSclTop = wrap.scrollTop;
          head = wrap.querySelector('.scl__head');
        }
        const wrapTop = (wrap !== window) ? this.$getOffset(wrap).top : 0;
        const headH = (head !== null) ? head.offsetHeight : 0;
        const sclTop = this.$getOffset(this.$el).top - wrapTop - headH;
        if (wrapSclTop > sclTop) {
          // wrap.scrollTo(0, sclTop);
          // wrap.dispatchEvent(new Event('scroll'));
          this.$scrollTo(wrapEl, { top: sclTop }, 300);
        }
        setTimeout(() => {
          this.linePosition();
        }, 100);
      }
    },
  },
  beforeCreate() {
    this.uuid = uuid.toString();
    uuid += 1;
  },
  created() {
  },
  mounted() {
    window.addEventListener('resize', this.linePosition);
    this.$nextTick(() => {
      this.$el.querySelector('.tablist').addEventListener('scroll', this.lineWrapLeftPosition);
      if (this.fixed) {
        let sclBody = this.$el.closest('.scl__body');
        if (sclBody === null)sclBody = window;
        sclBody.addEventListener('scroll', this.tabFixed);
        this.tabFixed();
        this.$once('hook:beforeDestroy', () => {
          sclBody.removeEventListener('scroll', this.tabFixed);
        });
      }
      let delay = 1;
      if (Number(this.uuid) === 0) delay = 1000;
      setTimeout(() => {
        this.linePosition();
      }, delay);
    });
  },
  destroyed() {
    this.$el.querySelector('.tablist').removeEventListener('scroll', this.lineWrapLeftPosition);
    window.removeEventListener('resize', this.linePosition);
  },
  methods: {
    lineWrapLeftPosition() {
      const { tablist } = this.$refs;
      const tablistLeft = tablist.scrollLeft;
      this.lineWrapLeft = -tablistLeft;
      this.isScrollableChk();
    },
    linePosition() {
      const $tablist = this.$refs.tablist;
      if ($tablist === undefined) return;
      const sclWidth = $tablist.scrollWidth;
      this.lineWrapWidth = sclWidth;
      let active = this.$refs.tablist.querySelector('.router-link-active');
      let activeParent = null;
      if (active === null) {
        active = this.$refs.tablist.querySelector('.active');
        activeParent = active;
      } else {
        activeParent = active.parentNode;
      }
      if (active === null) return;
      this.lineWidth = activeParent.offsetWidth;
      this.lineLeft = activeParent.offsetLeft;

      const tablistLeft = $tablist.scrollLeft;
      if (tablistLeft !== 0) this.lineWrapLeft = -tablistLeft;

      const sclLeft = this.lineLeft - (document.body.clientWidth / 2) + (this.lineWidth / 2);
      if (this.isAniamte === false) {
        this.isAniamte = true;
        // try {
        //   $tablist.scrollTo({
        //     left: sclLeft,
        //     behavior: 'smooth',
        //   });
        //   setTimeout(() => {
        //     this.isAniamte = false;
        //   }, 300);
        // } catch (error) {
        //   $tablist.scrollTo(sclLeft, 0);
        //   setTimeout(() => {
        //     this.isAniamte = false;
        //   }, 10);
        // }
        this.$scrollTo($tablist, { left: sclLeft }, 300, () => {
          this.isAniamte = false;
        });
      }
      this.isScrollableChk();
    },
    isScrollableChk() {
      const { tablist } = this.$refs;
      const tablistLeft = tablist.scrollLeft;
      const sclWidth = tablist.scrollWidth;
      if (this.$el.offsetWidth < sclWidth) {
        this.isScrollable = true;
      } else {
        this.isScrollable = false;
      }

      if (tablistLeft <= 16) {
        this.isScrollableLeft = false;
      } else {
        this.isScrollableLeft = true;
      }
      if (tablist.scrollWidth <= (tablistLeft + tablist.offsetWidth) + 16) {
        this.isScrollableRight = false;
      } else {
        this.isScrollableRight = true;
      }
    },
    tabFixed() {
      let wrap = this.$el.closest('.scl__body');
      if (wrap === null) wrap = window;
      if ((this.$el.closest('.page_wrap') !== null && !this.$el.closest('.page_wrap').classList.contains('lock')) || (wrap !== window && wrap.classList.contains('pop_body'))) {
      // if (!this.$el.closest('.page_wrap').classList.contains('lock') || wrap.classList.contains('pop_body')) {
        const elWrap = (wrap === window) ? document : wrap;
        const fixedEls = elWrap.querySelectorAll('.fixed');
        let fixedTop = 0;
        if (fixedEls.length) {
          fixedEls.forEach((i) => {
            // fixedTop += i.children[0].offsetHeight
            fixedTop += i.firstChild.offsetHeight;
          });
        }
        if (elWrap !== document) {
          const $prevEl = elWrap.previousElementSibling;
          if ($prevEl !== null) {
            const $prevClassList = $prevEl.classList;
            if ($prevClassList.contains('pop_head')) {
              fixedTop += $prevEl.firstChild.offsetHeight;
            }
          }
        }
        let margin = fixedTop;
        const height = this.$el.firstChild.offsetHeight;
        const sclTop = (wrap === window) ? wrap.scrollY : wrap.scrollTop;
        if (sclTop < this.lastScrollPosition) margin -= height;
        if (this.$getOffset(this.$el).top <= (sclTop + margin)) {
          this.isFixed = true;
          if (height > 0 && !this.isFixedTopChk) {
            this.fixedStyle = { top: `${fixedTop}px` };
            this.isFixedTopChk = true;
          }
        } else {
          this.isFixed = false;
          if (this.isFixedTopChk) {
            this.fixedStyle = null;
            this.isFixedTopChk = false;
          }
          setTimeout(() => {
            this.lastScrollPosition = sclTop;
          }, 1);
        }
      }
    },
  },
};
</script>
