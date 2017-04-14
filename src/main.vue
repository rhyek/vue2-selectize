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
    value() {
      this.setValue()
    },
    options (value, old) {
      if (this.$el.selectize && !equal(value, old)) {
        this.$el.selectize.clearOptions()
        this.$el.selectize.addOption(this.current)
        this.$el.selectize.refreshOptions(false)
        this.setValue()
      }
    }
  },
  methods: {
    track (nodes) {
      if (nodes)
        this.current = nodes
          .filter(node => node.tag && node.tag.toLowerCase() === 'option')
          .map(node => {
            return {
              text: node.children ? node.children[0].text.trim() : null,
              value: node.data.domProps ? node.data.domProps.value : node.data.attrs.value
            }
          })
      else
        this.current = []
    },
    setValue() {
      this.$el.selectize.setValue(this.value)
    }
  },
  render (c) {
    if (this.settings.options === undefined)
      this.track(this.$slots.default)
    return c(
      'select',
      this.$slots.default)
  }
}
</script>