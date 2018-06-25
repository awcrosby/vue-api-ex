<template>
  <div class="container">
    <app-post-list></app-post-list>
  </div>
</template>

<script>
  import PostList from './components/PostList.vue';
  export default {
    components: {
      'app-post-list': PostList
    }
  }
</script>

<style>
</style>
