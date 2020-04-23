<template>
  <section class="drum-select">
    <div class="drum-select__drums-container">
      <div
        v-if="leftLoading"
        class="drum-select__loader drum-select__loader--left"
      />
      <swiper
        v-else
        ref="leftSwiper"
        class="drum-select__drum-roll"
        :options="options"
        @slideChange="changeLeft"
      >
        <swiper-slide
          v-for="item in leftDrum"
          :key="item.text"
          class="drum-select__cell"
        >
          {{ item.text }}
        </swiper-slide>
      </swiper>

      <div
        v-if="rightLoading"
        class="drum-select__loader drum-select__loader--right"
      />
      <swiper
        v-else
        ref="rightSwiper"
        class="drum-select__drum-roll"
        :options="options"
        @slideChange="changeRight"
      >
        <swiper-slide
          v-for="item in rightDrum"
          :key="item.text"
          class="drum-select__cell"
        >
          <span class="drum-select__text">
            {{ item.text }}

            <img
              v-if="item.isFlash"
              src="./img/flash.svg"
              class="drum-select__flash"
            >
          </span>
        </swiper-slide>
      </swiper>
    </div>
  </section>
</template>

<script>
  import { swiper, swiperSlide } from 'vue-awesome-swiper';
  import 'swiper/dist/css/swiper.css';

  export default {
    name: 'DrumSelect',

    components: {
      swiper,
      swiperSlide
    },

    props: {
      leftDrum: {
        type: Array,
        required: true
      },

      rightDrum: {
        type: Array,
        required: true
      },

      value: {
        type: Array
      },

      rightLoading: {
        type: Boolean,
        default: false
      },

      leftLoading: {
        type: Boolean,
        default: false
      }
    },

    data: () => ({
      options: {
        direction: 'vertical',
        slidesPerView: 5,
        centeredSlides: true
      }
    }),

    watch: {
      value(val) {
        this.comparePosition();
      },

      leftDrum() {
        this.updateValue();
      },

      rightDrum() {
        this.updateValue();
      }
    },

    methods: {
      changeLeft(x) {
        let ind = this.$refs.leftSwiper.swiper.activeIndex,
          leftVal = this.leftDrum[ind].value;

        this.$emit('input', [ leftVal, this.value[1] ]);
        this.$emit('changeLeft', leftVal);
      },

      changeRight(x) {
        let ind = this.$refs.rightSwiper.swiper.activeIndex,
          rightVal = this.rightDrum[ind] ? this.rightDrum[ind].value : null;

        this.$emit('input', [ this.value[0], rightVal ]);
        this.$emit('changeRight', rightVal);
      },

      comparePosition() {
        this.$nextTick(() => {
          if (this.value[0] && this.$refs.leftSwiper) {
            let leftSliderIndex = this.$refs.leftSwiper.swiper.activeIndex;
            let leftDataIndex = this.getIndexByValue(
              this.value[0],
              this.leftDrum
            );

            if (leftSliderIndex !== leftDataIndex) {
              this.$refs.leftSwiper.swiper.slideTo(leftDataIndex);
            }
          }

          if (this.value[1] && this.$refs.rightSwiper) {
            let rightSliderIndex = this.$refs.rightSwiper.swiper.activeIndex;
            let rightDataIndex = this.getIndexByValue(
              this.value[1],
              this.rightDrum
            );

            if (rightSliderIndex !== rightDataIndex) {
              this.$refs.rightSwiper.swiper.slideTo(rightDataIndex);
            }
          }
        });
      },

      updateValue() {
        this.$nextTick(() => {
          this.$refs.leftSwiper.swiper.update();
          this.$refs.rightSwiper.swiper.update();
        });

        this.$emit('input', [
          this.value[0] || this.leftDrum[0].value,
          this.value[1] || this.rightDrum[0].value
        ]);
      },

      getIndexByValue(val, arr) {
        let ind = 0;

        arr.forEach((item, index) => {
          if (item.value === val) {
            ind = index;
            return false;
          }
        });

        return ind;
      }
    }
  };
</script>

<style lang="postcss">
  @import '@front-ui-kit/mixins.css';

  .drum-select {
    height: 200px;
    cursor: pointer;
    position: relative;
    z-index: 1;
    user-select: none;

    &__drums-container {
      display: flex;
      align-items: center;
      height: 100%;
      position: relative;

      &::after {
        content: '';
        height: 40px;
        border: 1px solid var(--brand-orange);
        position: absolute;
        left: 0;
        right: 0;
        border-right: 0;
        border-left: 0;
      }
    }

    &__drum-roll {
      width: 50%;
      height: 100%;
      position: relative;
      z-index: 2;

      &:after,
      &:before {
        content: '';
        position: absolute;
        left: 0;
        right: 0;
        background: linear-gradient(360deg, rgba(255, 255, 255, 0.47) 54.17%, rgba(255, 255, 255, 0) 100%);
        height: 60px;
        z-index: 3;
      }

      &::before {
        top: 0;
        transform: rotate(-180deg);
      }

      &::after {
        bottom: 0;
      }
    }

    &__cell {
      font-size: 16px;
      line-height: 140%;
      height: 40px;
      padding: 0 5px;
      display: flex;
      justify-content: center;
      text-align: center;
      align-items: center;
    }

    &__loader {
      width: 50%;
      z-index: 2;
      display: flex;
      align-items: center;
      justify-content: center;

      &:after {
        content: '';
        display: block;
        background: url('./img/loader.svg') center no-repeat;
        width: 16px;
        height: 16px;
        animation: spin 3s infinite linear;
      }

      &--left {
        left: 0;
      }

      &--right {
        left: 50%;
      }
    }

    &__flash {
      margin: -7px 0 0 7px;
      position: absolute;
      left: 100%;
      top: 50%;
    }

    &__text {
      position: relative;
    }
  }

  @keyframes spin {

    from {
      transform: rotate(0deg);
    }

    to {
      transform: rotate(360deg);
    }
  }
</style>
