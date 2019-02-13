<template>
  <section id="app" class="hero is-info is-fullheight is-bold">
    <div class="hero-body">

      <Timer />
      <Selection />
    </div>
  </section>
</template>

<script>
import Selection from '@/components/Selection.vue'
import Timer from '@/components/Timer.vue'

export default {
  name: 'App',
  components: {
    Timer,
    Selection
  }
}
</script>

<style>

</style>
