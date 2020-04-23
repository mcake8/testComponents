<template>
  <div
    :class="[
      'ui-select',
      {
        'ui-select--open': isOpen
      }
    ]"
    v-clickOutside="closeSelect"
  >
    <div class="ui-select__label" @click="toggleOpen">
      <template v-if="selectedItem">
        {{ selectedItem[itemText] }}
      </template>
      <span class="ui-select__placeholder" v-else>
        Выберите...
      </span>
    </div>
    <ui-popup :isShown="isOpen && isMobile" @close="toggleOpen" down menu-wrap>
      <select-list
        :list="options"
        v-model="selectedItem"
        :item-text="itemText"
        :empty-text="emptyText"
      />
    </ui-popup>
    <transition name="ui-select-show">
      <div class="ui-select__list" v-if="isOpen && !isMobile">
        <select-list
          :style="{maxHeight: listHeight + 'px' }"
          :list="options"
          v-model="selectedItem"
          :item-text="itemText"
          :empty-text="emptyText"
        />
      </div>
    </transition>
  </div>
</template>

<script>
  import clickOutside from "@src/directives/clickOutside";
  import UiPopup from "@src/components/Popup/index";
  import SelectList from "./SelectList";
  import isDesktop from "@src/mixins/isDesktop";

  export default {
    name: "UiSelect",
    mixins: [isDesktop],

    props: {
      //Массив опций выбора
      options: {
        type: Array,
        default: () => ([])
      },

      value: {
        default: null
      },

      emptyText: {
        type: String,
        default: 'Не найдено'
      },

      //Ключ текстового лейбла в опции выбора
      itemText: {
        type: String,
        default: "text"
      },

      //Ключ значения в опции выбора
      itemValue: {
        type: String,
        default: "value"
      },

      //Разрешить селекту не иметь выбранного значения
      allowEmpty: {
        type: Boolean,
        default: false
      },

      //Возвращать обьект целиком вместо значения
      returnObject: {
        type: Boolean,
        default: false
      },

      maxHeight: {
        type: String,
        default: null
      }
    },

    data: vm => ({
      isOpen: false,
      listHeight: vm.maxHeight || 168,
      isMobile: !vm.isDesktop()
    }),

    mounted() {
      if (!this.allowEmpty && !this.value) {
        this.$emit(
          "input",
          this.returnObject ? this.options[0] : this.options[0][this.itemValue]
        );
      }
    },

    computed: {
      selectedItem: {
        get() {
          if (this.value && typeof this.value === "object") {
            return this.options.find(
              i => i[this.itemValue] === this.value[this.itemValue]
            );
          }
          return this.options.find(i => i[this.itemValue] === this.value);
        },
        set(value) {
          this.$emit(
            "input",
            this.returnObject ? value : value[this.itemValue]
          );
          this.closeSelect();
        }
      }
    },

    methods: {
      toggleOpen() {
        this.isOpen = !this.isOpen;
      },

      closeSelect() {
        this.isOpen = false;
      }
    },

    directives: {
      clickOutside
    },

    components: {
      UiPopup,
      SelectList
    }
  };
</script>

<style lang="postcss" scoped>
  @import "@styles/mixins.css";
  .ui-select {
    position: relative;
    cursor: pointer;
    font-size: 16px;

    &--open {
      .ui-select__label {
        border-radius: var(--borr2) var(--borr2) 0px 0px;
        z-index: 3;
        position: relative;
        &:after {
          transform: rotate(180deg);
        }
      }
    }

    &__label {
      height: 46px;
      line-height: 22px;
      background: #ffffff;
      border: 1px solid var(--border-grey);
      border-radius: var(--borr2);
      padding: 12px 50px 12px 20px;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
      &::after {
        content: "";
        @mixin svg-in angle-down;
        width: 21px;
        height: 10px;
        position: absolute;
        right: 20px;
        top: 19px;
        transition: var(--transition);
      }
    }

    &__list {
      border: 1px solid var(--border-grey);
      border-top: 0;
      border-radius: 0px 0px var(--borr2) var(--borr2);
      position: absolute;
      top: 100%;
      background-color: #fff;
      transition: .1s;
      width: 100%;
      z-index: 3;
      transform: translateY(0px);
      opacity: 1;
    }

    &-show-enter-active,
    &-show-leave-active {
      transform: translateY(-10px);
      opacity: 0;
    }
  }
</style>
