<template>
  <div
    class="ui-swiper-wrap"
    :class="{ full: fullWidth, auto: autoWidth, only: isOnly }"
  >
    <div
      v-swiper:mySwiper="swiperOption"
      class="ui-swiper"
      :class="{ loop: loop, 'auto-height': autoHeight }"
      :dir="dir"
      @ready="swiperReady"
      @clickSlide="swiperClickSlide"
      @beforeDestroy="swiperBeforeDestroy"
      @slideChange="swiperSlideChange"
      @slideChangeTransitionStart="swiperSlideChangeTransitionStart"
      @slideChangeTransitionEnd="swiperSlideChangeTransitionEnd"
      @slideNextTransitionStart="swiperSlideNextTransitionStart"
      @slideNextTransitionEnd="swiperSlideNextTransitionEnd"
      @slidePrevTransitionStart="swiperSlidePrevTransitionStart"
      @slidePrevTransitionEnd="swiperSlidePrevTransitionEnd"
      @transitionStart="swiperTransitionStart"
      @transitionEnd="swiperTransitionEnd"
      @touchStart="swiperTouchStart"
      @touchMove="swiperTouchMove"
      @touchMoveOpposite="swiperTouchMoveOpposite"
      @sliderMove="swiperSliderMove"
      @touchEnd="swiperTouchEnd"
      @click="swiperClick"
      @tap="swiperTap"
      @doubleTap="swiperDoubleTap"
      @imagesReady="swiperImagesReady"
      @progress="swiperProgress"
      @reachBeginning="swiperReachBeginning"
      @reachEnd="swiperReachEnd"
      @fromEdge="swiperFromEdge"
      @setTranslate="swiperSetTranslate"
      @setTransition="swiperSetTransition"
      @resize="swiperResize"
      @observerUpdate="swiperObserverUpdate"
      @beforeLoopFix="swiperBeforeLoopFix"
      @loopFix="swiperLoopFix"
    >
      <div class="swiper-wrapper">
        <slot />
      </div>
      <div ref="paginationWrap" class="swiper-pagination-wrap">
        <div slot="pagination" ref="pagination" class="swiper-pagination" />
        <button
          v-if="autoplay"
          type="button"
          class="swiper-auto-ctl"
          :class="{ play: !isAutoplay }"
          :aria-label="autoplayText"
          @click="autoPlayButton"
        />
      </div>
      <button
        v-if="navi"
        slot="button-prev"
        ref="buttonPrev"
        type="button"
        aria-label="이전 슬라이드"
        class="swiper-button-prev swiper-button"
        @click="swiperPrevEvt"
      >
        이전 슬라이드
      </button>
      <button
        v-if="navi"
        slot="button-next"
        ref="buttonNext"
        type="button"
        aria-label="다음 슬라이드"
        class="swiper-button-next swiper-button"
        @click="swiperNextEvt"
      >
        다음 슬라이드
      </button>
    </div>
  </div>
</template>
<script>
import uiEventBus from '../uiEventBus.vue';

// Import Swiper styles
export default {
  name: 'kbSwiper',
  props: {
    value: { type: [String, Number], default: null },
    navi: { type: Boolean, default: false },
    fullWidth: { type: Boolean, default: false },
    autoWidth: { type: Boolean, default: false },
    autoHeight: { type: Boolean, default: false },
    loop: { type: Boolean, default: false },
    autoplay: { type: Boolean, default: false },
    delay: { type: Number, default: 3000 },
    index: { type: [String, Number], default: 0 },
    dir: { type: String, default: null },
    pagingType: { type: String, default: 'bullets' },
  },
  data() {
    return {
      isAutoplay: false,
      isOnly: false,
      isChagned: false,
      resizeUpadte: '',
      timer: null,
    };
  },
  computed: {
    autoplayText() {
      let txt = '슬라이드 자동롤링 ';
      txt += this.isAutoplay ? '중지' : '시작';
      return txt;
    },
    swiperOption() {
      let autoplayOpt = '';
      let autoHeightOpt = false;
      if (this.autoplay) {
        autoplayOpt = {
          delay: this.delay,
          // disableOnInteraction: false
        };
      }
      if (this.autoHeight) autoHeightOpt = true;
      return {
        slidesPerView: 'auto',
        loop: !!this.loop,
        autoplay: autoplayOpt,
        autoHeight: autoHeightOpt,
        initialSlide: Number(this.index),
        pagination: {
          el: '.swiper-pagination',
          type: this.pagingType,
          clickable: true,
          renderBullet(index, className) {
            return `<button type="button" class="${className}">${index
              + 1}번째 슬라이드</button>`;
          },
        },
      };
    },
  },
  watch: {
    value() {
      if (
        this.value !== null
        && this.value !== ''
        && !Number.isNaN(this.value)
      ) {
        if (!this.isChagned) this.mySwiper.slideTo(Number(this.value), 100);
        this.$emit('input', this.value);
        this.isChagned = false;
      }
    },
  },
  mounted() {
    // console.log(this.$children);
    if (this.autoplay) {
      this.isAutoplay = true;
      // this.mySwiper.slideTo(this.index, 0, false);
      // this.mySwiper.autoplay.start();
    }

    uiEventBus.$on('kbSwiperUpdate', this.swiperUpdate);
    // if (this.index > 0) {
    //     this.mySwiper.slideTo(this.index, 0, false)
    // }
  },
  destroyed() {
    uiEventBus.$off('kbSwiperUpdate', this.swiperUpdate);
  },
  methods: {
    swiperUpdate() {
      setTimeout(() => {
        this.mySwiper.update();
      }, 20);
    },
    swiperReady(swiper) {
      // console.log('swiperReady');
      this.$nextTick(() => {
        if (!this.loop && this.navi) this.swiperCheck(swiper);
        if (
          this.value !== null
          && this.value !== ''
          && !Number.isNaN(this.value)
        ) {
          this.mySwiper.slideTo(Number(this.value), 0);
        }
        if (this.autoWidth) {
          setTimeout(() => {
            this.mySwiper.update();
          }, 500);
        }
      });
    },
    swiperClickSlide(index) {
      console.log('Click slide!', index);
    },
    swiperBeforeDestroy() {
      // console.log('swiperBeforeDestroy');
    },
    swiperSlideChange() {
      // console.log('swiperSlideChange');
    },
    swiperSlideChangeTransitionStart() {
      // console.log('swiperSlideChangeTransitionStart');
    },
    swiperSlideChangeTransitionEnd() {
      if (this.value !== null && !this.isChagned) {
        this.isChagned = true;
        clearTimeout(this.timer);
        if (this.value !== this.mySwiper.realIndex) {
          this.$emit('input', this.mySwiper.realIndex);
        }
        this.timer = setTimeout(() => {
          this.isChagned = false;
        }, 10);
      }
      this.$emit('swiperChange', this.mySwiper.snapIndex);
    },
    swiperSlideNextTransitionStart() {
      // console.log('swiperSlideNextTransitionStart');
    },
    swiperSlideNextTransitionEnd() {
      // console.log('swiperSlideNextTransitionEnd');
    },
    swiperSlidePrevTransitionStart() {
      // console.log('swiperSlidePrevTransitionStart');
    },
    swiperSlidePrevTransitionEnd() {
      // console.log('swiperSlidePrevTransitionEnd');
    },
    swiperTransitionStart() {
      // console.log('swiperTransitionStart');
      if (!this.loop && this.navi) this.swiperCheck(this.mySwiper);
    },
    swiperTransitionEnd() {
      // console.log('swiperTransitionEnd');
    },
    swiperTouchStart() {
      // console.log('swiperTouchStart');
    },
    swiperTouchMove() {
      // console.log('swiperTouchMove');
    },
    swiperTouchMoveOpposite() {
      // console.log('swiperTouchMoveOpposite');
    },
    swiperSliderMove() {
      // console.log('swiperSliderMove');
    },
    swiperTouchEnd(e) {
      const $target = e.target;
      if (
        !$target.classList.contains('swiper-pagination-wrap')
        && !$target.classList.contains('swiper-auto-ctl')
        && !$target.classList.contains('swiper-pagination')
        && !$target.classList.contains('swiper-pagination-bullet')
      ) {
        if (this.autoplay) this.isAutoplay = false;
      }
    },
    swiperClick() {
      // console.log('swiperClick');
    },
    swiperTap() {
      // console.log('swiperTap');
    },
    swiperDoubleTap() {
      // console.log('swiperDoubleTap');
    },
    swiperImagesReady() {
      // console.log('swiperImagesReady');
    },
    swiperProgress() {
      // console.log('swiperProgress');
    },
    swiperReachBeginning() {
      // console.log('swiperReachBeginning');
    },
    swiperReachEnd() {
      // console.log('swiperReachEnd');
    },
    swiperFromEdge() {
      // console.log('swiperFromEdge');
    },
    swiperSetTranslate() {
      // console.log('swiperSetTranslate');
      if (!this.loop && this.navi) this.swiperCheck(this.mySwiper);
    },
    swiperSetTransition() {
      // console.log('swiperSetTransition');
    },
    swiperResize() {
      if (!this.loop && this.navi) this.swiperCheck(this.mySwiper);
      clearTimeout(this.resizeUpadte);
      this.resizeUpadte = setTimeout(() => {
        this.mySwiper.update();
      }, 300);
    },
    swiperObserverUpdate() {
      // console.log('swiperObserverUpdate');
    },
    swiperBeforeLoopFix() {
      // console.log('swiperBeforeLoopFix');
    },
    swiperLoopFix() {
      // console.log('swiperLoopFix');
    },
    swiperCheck(tar) {
      // console.log(tar.activeIndex, tar.realIndex, tar.snapIndex)
      // const $length = tar.slides.length;
      const $length = tar.pagination.bullets.length
        ? tar.pagination.bullets.length
        : tar.slides.length;
      // const $index = tar.realIndex
      const $index = tar.snapIndex;
      const btnPrev = this.$refs.buttonPrev;
      const btnNext = this.$refs.buttonNext;

      if (!this.loop && this.navi) {
        if ($index === 0) {
          btnPrev.classList.add('swiper-button-disabled');
          btnPrev.disabled = true;
        } else {
          btnPrev.classList.remove('swiper-button-disabled');
          btnPrev.disabled = false;
        }
        // console.log($index, ($length - 1))
        if ($index === $length - 1) {
          btnNext.classList.add('swiper-button-disabled');
          btnNext.disabled = true;
        } else {
          btnNext.classList.remove('swiper-button-disabled');
          btnNext.disabled = false;
        }
      }

      // const $paginationWrap = this.$refs.paginationWrap
      // if ($length <= 1) {
      //     this.isOnly = true
      //     $paginationWrap.classList.add('hide')
      // } else {
      //     this.isOnly = false
      //     $paginationWrap.classList.remove('hide')
      // }
    },
    swiperPrevEvt() {
      this.mySwiper.slidePrev();
    },
    swiperNextEvt() {
      this.mySwiper.slideNext();
    },
    autoPlayButton(e) {
      e.stopPropagation();
      this.isAutoplay = !this.isAutoplay;
      if (this.isAutoplay) {
        this.mySwiper.autoplay.start();
      } else {
        this.mySwiper.autoplay.stop();
      }
    },

  },
};
</script>
