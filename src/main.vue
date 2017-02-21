<template>
  <select>
    <slot></slot>
  </select>
</template>

<script>
import $ from 'jquery'

if (!$().selectize) {
  require('selectize')
}

export default {
  props: {
    value: {
      default: ''
    },
    settings: {
      type: Object,
      default() { return {} }
    }
  },
  mounted() {
    $(this.$el).selectize({
      onInitialize: () => {
        this.setValue()
      },
      onChange: value => {
        this.$emit('input', value)
      },
      ...this.settings
    })
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