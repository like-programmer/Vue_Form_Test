<template>
  <div class="page-header">
    <div class="page-header__container">
      <div class="page-header__section">
        <span class="page-header__font page-header__font--section">{{userData?.username}} ({{userData?.name}})</span>
      </div>
      <div class="page-header__section">
        <span class="page-header__font page-header__font--section">{{userData?.phone}}</span>
      </div>
      <div class="page-header__section">
        <span class="page-header__font page-header__font--section">{{userData?.email}}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PageHeader",
  props: {
    userData: {
      type: Object,
      default: () => ({})
    }
  }
}
</script>
