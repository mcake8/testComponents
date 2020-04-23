<template>
  <transition :name="`ui-alert-${ainmation}`" v-if="isShown" appear>
    <div
      :style="{
        top: `${topVal}px`
      }"
      :class="[
        'ui-alert',
        {
          'ui-alert--success': color === 'success' || color === 'green',
          'ui-alert--error': color === 'error' || color === 'red'
        }
      ]"
      ref="uiAlert"
    >
      <div class="ui-alert__close" @click="closeAlert"></div>
      <div class="text-p">
        <slot></slot>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: "UiAlert",

    props: {
      color: {
        type: String,
        default: ""
      },

      isShown: {
        type: Boolean,
        default: false
      },

      timeout: {
        type: [String, Number],
        default: 3000
      },

      top: {
        type: Number,
        default: 10
      },

      autoTop: {
        type: Boolean,
        default: true
      },

      autoTopAnimate: {
        type: Boolean,
        default: true
      },

      ainmation: {
        type: String,
        default: "slideY"
      },

      initTimer: {
        type: Boolean,
        default: false
      }
    },

    data: vm => ({
      topVal: vm.top,
      closeTimeout: null
    }),

    methods: {
      closeAlert() {
        this.topVal = this.top;
        clearTimeout(this.closeTimeout);
        this.$emit("close");
      },

      setTimer() {
        let timeout = +this.timeout;

        clearTimeout(this.closeTimeout);

        if (timeout && timeout > 0) {
          this.closeTimeout = setTimeout(() => {
            this.closeAlert();
          }, timeout);
        }
      },

      checkPostion() {
        let existElms = document.querySelectorAll(".ui-alert"),
          existTop = this.topVal;

        for (let i = 0; i < existElms.length; i++) {
          existTop += existElms[i].offsetHeight + this.topVal;
        }

        this.$nextTick(() => {
          let elms = document.querySelectorAll(".ui-alert"),
            nodeArr = Array.prototype.slice.call(elms),
            currItem = this.$refs.uiAlert,
            ind = nodeArr.indexOf(currItem),
            top = this.topVal;

          if (elms.length > 1) {
            for (let i = 0; i < ind; i++) {
              top += elms[i].offsetHeight + this.topVal;
            }

            if (ind < 1 && existElms.length !== elms.length) {
              top += existTop;
            }

            if (this.autoTopAnimate) {
              setTimeout(() => {
                this.topVal = top;
              }, 200);
            } else {
              this.topVal = top;
            }
          }
        });
      }
    },

    mounted() {
      this.checkPostion();

      if (this.initTimer) {
        this.setTimer();
      }
    },

    watch: {
      isShown(val) {
        this.checkPostion();
        this.setTimer();
      }
    }
  };
</script>

<style lang="postcss" scoped>
  @import "@styles/mixins.css";
  .ui-alert {
    position: fixed;
    right: 10px;
    top: 10px;
    left: 10px;
    max-width: 100%;
    padding: 20px 40px 20px 20px;
    color: #fff;
    background: var(--brand-orange);
    border-radius: var(--borr2);
    z-index: 99;
    opacity: 1;
    transition: var(--transition);
    transform: translate(0, 0);

    @media (--tablet) {
      left: auto;
      max-width: 400px;
    }

    @media (--desktop) {
      max-width: 600px;
    }

    &--success {
      background-color: var(--hightlight-green);
    }

    &--error {
      background-color: var(--error-red);
    }

    &__close {
      position: absolute;
      right: 10px;
      top: 10px;
      width: 16px;
      height: 16px;
      cursor: pointer;
      @mixin svg-in cross, %23ffffff;

      @media (--tablet) {
        top: 10px;
        right: 10px;
      }

      @media (--desktop) {
        &:hover {
          opacity: 0.9;
        }
      }
    }

    &-fade {
      &-enter-active,
      &-leave-active {
        opacity: 0;
      }
    }

    &-slideX {
      &-enter-active,
      &-leave-active {
        opacity: 0;
        transform: translateX(100%);
      }
    }

    &-slideY {
      &-enter-active,
      &-leave-active {
        opacity: 0;
        transform: translateY(-100%);
      }
    }
  }
</style>
