<template>
  <select :value="selected" @change="onChange">
    <option v-for="(option, index) in options" :key="index" :value="option">{{option}}</option>
  </select>
</template>

<script>
export default {
  name: "SelectItem",
  props: {
    options: {
      type: Array,
      default: () => []
    },
    selected: {
      type: [String, Number],
      default: ''
    },
  },
  methods: {
    onChange(e) {
      this.$emit('on-select', e.target.value)
    }
  },
}
</script>
