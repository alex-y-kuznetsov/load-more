<template>
  <div class="home">
    <LoadMore />
  </div>
</template>

<script>
import LoadMore from '@/components/LoadMore.vue'

export default {
  name: 'Home',
  components: {
    LoadMore
  }
}
</script>

<style scoped lang="less">
.home {
  height: 100%;
}
</style>
