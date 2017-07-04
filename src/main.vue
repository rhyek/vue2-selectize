<template>
  <div>
    <select ref="select">
      <slot/>
    </select>
  </div>
</template>

<script>
import $ from 'jquery'
import equal from 'deep-equal'

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
      default: () => ({})
    }
  },
  data () {
    return {
      current: null
    }
  },
  mounted() {
    $(this.$refs.select).selectize({
      onInitialize: () => {
        this.setValue()
      },
      onChange: value => {
        this.$emit('input', value)
      },
      ...this.settings
    })
  },
  computed: {
    options () {
      if (this.current != null)
        return this.current
          .map(option => ({
            text: option.text,
            value: option.value
          }))
    }
  },
  watch: {
    value(value, old) {
      if (!equal(value, old)) {
        this.setValue()
      }
    },
    options (value, old) {
      if (this.$refs.select.selectize && !equal(value, old)) {
        this.$refs.select.selectize.clearOptions()
        this.$refs.select.selectize.addOption(this.current)
        this.$refs.select.selectize.refreshOptions(false)
        this.setValue()
      }
    }
  },
  methods: {
    track (nodes) {
      if (nodes) {
        this.current = nodes
          .filter(node => node.tag && node.tag.toLowerCase() === 'option')
          .map(node => {
            return {
              text: node.children ? node.children[0].text.trim() : null,
              value: node.data.domProps ? node.data.domProps.value : node.data.attrs.value
            }
          })
      }
      else {
        this.current = []
      }
    },
    setValue () {
      this.$refs.select.selectize.setValue(this.value)
    }
  },
  beforeUpdate () {
    if (this.settings.options === undefined)
      this.track(this.$slots.default)
  }
}
</script>