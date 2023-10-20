<template>
  <button class="button" :class="{
    'button--primary': btnStyle === 'primary',
    'button--secondary': btnStyle === 'secondary',
  }" type="button" @click="$emit('click')">
    <span class="button__font button__font--button">
      <slot></slot>
    </span>
  </button>
</template>

<script>

export default {
  name: "ButtonItem",
  props: {
    btnStyle: {
      type: String,
      default: 'primary'
    },
  },
}
</script>
