<template>
  <div class='cell' @click='activate' :class='{active: isActive}'>
  </div>
</template>

<script>
export default {
  name: 'Cell',
  data () {
    return {
      isActive: false
    }
  },
  methods: {
    activate () {
      this.isActive = !this.isActive
      this.$emit('toggle-activated')
    }
  }
}
</script>

<style scoped>
  .cell {
    //width: 20px;
    //height: 20px;
    //margin: 2px;
    border: 1px solid black;
  }
  .active {
      background: black;
  }
</style>
