<template>
  <div class="product-filter">
    <h4>
      {{ $t(filterIndex + '_filter') }}
      {{ availableFilterOptions }}
    </h4>
    <div v-if="filterIndex === 'color'">
      <color-selector
        context="category"
        :attribute_code="filter.attribute_code"
        code="color"
        v-for="(color, index) in filter.options"
        :key="index"
        :id="color.id"
        :label="color.label"
      />
    </div>
    <div v-else-if="filter.frontend_input==='price'">
      <price-slider
        context="category"
        code="price"
        id="price"
        :price-range="filter.options"
        content="Price "
        label="Price Label"
      />
    </div>
    <div
      :class="{'filter-options': showFilterExpander(), 'filter-expanded': filterExpand}"
      v-else-if="isSelector(filter.frontend_input)"
    >
      <div
        class="filter-option"
        v-for="(filterOption, index) in filter.options"
        :key="index"
      >
        <selector
          context="category"
          :filter-type="filter.frontend_input"
          :attribute_code="filter.attribute_code"
          :code="filter.attribute_code"
          :id="filterOption.id"
          :label="filterOption.label"
        />
      </div>
      <div class="filter-expander" v-show="showFilterExpander()">
        <span
          class="filter-expand"
          @click="filterExpander()"
        >
          <div class="plus-minus">
            <div class="horizontal" />
            <div class="vertical" />
          </div>
          <span class="label">
            {{ filterExpanderMessage }}
          </span>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import rootStore from '@vue-storefront/core/store'
import GenericSelector from './FilterTypes/GenericSelector'
import ColorSelector from './FilterTypes/ColorSelector'
import Selector from './FilterTypes/Selector'
import PriceSlider from './FilterTypes/PriceSlider'
import i18n from '@vue-storefront/i18n'
import pickBy from 'lodash-es/pickBy'

export default {
  name: 'ProductFilter',
  props: {
    filterIndex: {
      type: String,
      required: true
    },
    filter: {
      type: Object,
      required: true
    },
    limit: {
      type: Number,
      required: false,
      default: 10
    }
  },
  components: {
    GenericSelector,
    ColorSelector,
    Selector,
    PriceSlider
  },
  beforeMount () {
    this.$bus.$on('filter-option-visibilty', this.filterOptionVisibilty)
  },
  beforeDestroy () {
    this.$bus.$off('filter-option-visibilty', this.filterOptionVisibilty)
  },
  data () {
    return {
      selectorFilterTypes: ['select', 'multiselect'],
      filterExpand: false,
      filterOptionVisibiltyData: []
    }
  },
  computed: {
    ...mapGetters(
      'category', ['getCurrentCategory', 'getActiveCategoryFilters', 'getCurrentCategoryProductQuery', 'getAvailableCategoryFilters']
    ),
    category () {
      return this.getCurrentCategory
    },
    currentProductList () {
      return this.$store.state.product.list.items
    },
    productCount () {
      return this.currentProductList.length
    },
    activeFilters () {
      return this.getActiveCategoryFilters
    },
    remainingFilterOptions () {
      return this.filter.options.length - this.limit
    },
    filterExpanderMessage () {
      return (this.filterExpand) ? i18n.t('Show less filter options') : (this.remainingFilterOptions > 1) ? i18n.t('Show {remainingFilterOptions} more filter options', { remainingFilterOptions: this.remainingFilterOptions }) : i18n.t('Show {remainingFilterOptions} more filter option', { remainingFilterOptions: this.remainingFilterOptions })
    },
    availableFilterOptions () {
      const getAvailableFilterOptions = pickBy(this.filter.options, (filterOption) => { return (filterOption.display) })
      return Object.keys(getAvailableFilterOptions).length
    }
  },
  methods: {
    isSelector (filterType) {
      return this.selectorFilterTypes.includes(filterType)
    },
    showFilterExpander () {
      return (this.availableFilterOptions > this.limit)
    },
    filterExpander () {
      this.filterExpand = !this.filterExpand
    },
    filterOptionVisibilty (filterOptionData) {
      console.log(filterOptionData)
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~theme/css/variables/colors';
@import '~theme/css/helpers/functions/color';
$primary-color: color(primary, $colors-theme);
$puerto-rico-color: color(puerto-rico);
$white-color: color(white);
$white-smoke-color: color(white-smoke);

h4 {
  margin-bottom: 10px;
}

.filter-options {
  max-height: 300px;
  transition: max-height .3s ease-in;
  overflow: hidden;
  padding-bottom: 30px;
  position: relative;
  .filter-expander {
    height: 30px;
    width: 100%;
    position: absolute;
    bottom: 0;
    background-color: #fff;
    span {
      line-height: 30px;
      cursor: pointer;
      &.label {
        padding-left: 5px;
        text-decoration: underline;
        font-weight: 700;
        font-size: 14px;
        color: $puerto-rico-color;
      }
    }
  }
  &.filter-expanded {
    max-height: 10000px;
  }
}

.plus-minus {
  position: relative;
  display: inline-block;
  width: 10px;
  height: 10px;
  top: 0;
  .vertical {
    transition: all 0.2s ease-in-out;
    transform: rotate(-90deg);
  }
  .horizontal {
    transition: all 0.2s ease-in-out;
    transform: rotate(-90deg);
    opacity: 1;
  }
}
.plus-minus .horizontal {
  position: absolute;
  background-color: $puerto-rico-color;
  width: 10px;
  height: 2px;
  left: 50%;
  margin-left: -5px;
  top: 50%;
  margin-top: -1px;
}
.plus-minus .vertical {
  position: absolute;
  background-color: $puerto-rico-color;
  width: 2px;
  height: 10px;
  left: 50%;
  margin-left: -1px;
  top: 50%;
  margin-top: -5px;
}
.filter-expanded {
  opacity: 1;
  .vertical {
    transition: all 0.2s ease-in-out;
    transform: rotate(90deg);
  }
  .horizontal {
    transition: all 0.2s ease-in-out;
    transform: rotate(90deg);
    opacity: 0;
  }
}
</style>