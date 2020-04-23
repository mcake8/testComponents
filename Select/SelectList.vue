<template>
  <ul class="scroll-list">
    <li class="menu-line menu-line--disabled" v-if="!list || !list.length ">{{emptyText}}</li>
    <li
      v-for="item in list"
      :key="item.value"
      class="menu-line"
      @click="$emit('input', item);"
    >
      {{ item[itemText] }}
    </li>
  </ul>
</template>

<script>
  import PerfectScrollbar from 'perfect-scrollbar';

  export default {
    name: 'CustomSelectScroll',

    props: {
      list: {
        type: Array,
        default: null
      },

      emptyText: {
        type: String,
        required: true
      },

      itemText: {
        type: String,
        default: 'text'
      },
    },

    data: () => ({
      ps: null
    }),

    mounted() {
      this.ps = new PerfectScrollbar(this.$el);
    },
  };
</script>


<style lang="postcss">
@import "../../../node_modules/perfect-scrollbar/css/perfect-scrollbar.css";
@import "./ps.css";

.scroll-list{
  position: relative;
}
</style>