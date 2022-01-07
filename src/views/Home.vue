<template>
  <div class="home">
    <LoadMore />
  </div>
</template>

<script>
// @ is an alias to /src
import LoadMore from '@/components/LoadMore.vue'

export default {
  name: 'Home',
  components: {
    LoadMore
  }
}
</script>
