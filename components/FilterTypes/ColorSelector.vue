<template>
  <div
    class="inline-flex"
    v-if="showFilterOption"
  >
    <button
      :class="['mr10 mb5 bg-cl-transparent brdr-1 brdr-circle brdr-cl-transparent :brdr-cl-bg-primary relative pointer color', active ? 'active' : '']"
      @click="switchFilter(id, label)"
      :aria-label="label"
    >
      <span
        class="absolute brdr-circle brdr-1 brdr-cl-secondary block color-inside"
        :style="colorFrom(label)"
      />
    </button>
  </div>
</template>

<script>
import Filter from './Filter'
import rootStore from '@vue-storefront/core/store'

export default {
  methods: {
    colorFrom (label) {
      if (rootStore.state.config.products.colorMappings) {
        if (typeof rootStore.state.config.products.colorMappings[label] !== 'undefined') {
          label = rootStore.state.config.products.colorMappings[label]
        }
      }
      if (label.indexOf('/') >= 0) label = label.replace('/', ',') // to be honest - this is a hack for colors like "ink/white"
      if (label && label.toString().indexOf(',') >= 0) {
        return 'background: linear-gradient(' + label + ')'
      } else {
        return 'background-color: ' + label
      }
    }
  },
  mixins: [Filter]
}
</script>

<style lang="scss" scoped>
  @import '~theme/css/variables/colors';
  @import '~theme/css/helpers/functions/color';
  $color-active: color(primary);

  .color {
    width: 40px;
    height: 40px;

    &.active {
      border-color: $color-active;
    }
  }

  .color-inside {
    width: 34px;
    height: 34px;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%)
  }
</style>
