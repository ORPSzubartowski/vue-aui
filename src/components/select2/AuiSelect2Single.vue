<template>
    <span>
        <input :disabled="disabled" type="hidden" ref="input">
        <slot></slot>
    </span>
</template>

<script>
  export default {
    props: {
      allowClear: Boolean,
      disabled: Boolean,
      dropdownAutoWidth: Boolean,
      initSelection: Function,
      placeholder: String,
      query: Function,
      value: String,
      width: String
    },

    data: function () {
      return {
        options: []
      }
    },

    created: function () {
      this.updateOptions()
      this.$on('optionsChanged', this.updateOptions)
    },

    mounted: function () {
      this.$refs.input.className = this.$el.className
      this.$el.className = ''

      this.$input = AJS.$(this.$refs.input)
      this.$input.val(this.value)
      this.$input.auiSelect2({
        allowClear: this.allowClear,
        query: this.query,
        initSelection: this.initSelection,
        data: () => ({results: this.options}),
        placeholder: this.placeholder,
        width: this.width,
        dropdownAutoWidth: this.dropdownAutoWidth
      });
      this.$input.on('change', this.onSelect2ValueChanged)
    },

    watch: {
      value: function (value) {
        this.$input.auiSelect2('val', value)
      },
      disabled() {
        this.$input.auiSelect2('val', this.value)
      }
    },

    methods: {
      onSelect2ValueChanged: function (event) {
        this.$emit('input', event.val)
      },

      updateOptions() {
        this.options = this.$children.map(child => {
          const id = child.value
          const text = child.text
          return {id, text}
        })
        if (this.$input) {
          this.$input.auiSelect2('val', this.value);
        }
      }
    }
  }
</script>

<style>
  .aui-select2-container.select2-container.select2-allowclear .select2-choice abbr {
    display: block;
  }
</style>
