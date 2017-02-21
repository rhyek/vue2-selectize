<template>
  <select>
    <slot></slot>
  </select>
</template>

<script>
import $ from 'jquery'

export default {
  props: {
    value: {
      type: [String, Number, Object],
      default: ''
    },
    settings: {
      type: Object,
      default() { return {} }
    }
  },
  mounted() {
    //console.log('selectize', $)
    // jQuery(this.$el).selec, {
    //   onInitialize: () => {
    //     this.setValue()
    //   },
    //   onChange: value => {
    //     this.$emit('input', value)
    //   },
    //   ...this.settings
    // })
  },
  watch: {
    value() {
      this.setValue()
    }
  },
  methods: {
    setValue() {
      this.$el.selectize.setValue(this.value)
    }
  }
}
</script>