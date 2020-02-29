<template>
  <div class="home">
    <h1>TODOS</h1>
    <Parent />
  </div>
</template>

<script>
// @ is an alias to /src
import Parent from '@/components/Parent.vue'

export default {
  name: 'home',
  components: {
    Parent
  }
}
</script>
