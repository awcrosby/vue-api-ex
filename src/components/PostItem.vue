<template>
    <li class="list-group-item" @click="postSelected" style="cursor: pointer">
      <span>Post id {{ post.id }}: {{ post.title | truncate }}</span>
    </li>
</template>

<script>
  import { eventBus } from '../main.js';
  export default {
    props: {
      post: {type: Object, required: true}
    },
    methods: {
      postSelected() {
        // emit selection to eventBus
        eventBus.$emit('postSelected', this.post);
      }
    },
    filters: {
      truncate(value) {
        if (!value) return '';
        value = value.toString();
        if (value.length <= 50) return value;
        return value.toString().substring(0, 50) + "..."
      }
    }
  }
</script>

<style scoped lang="css">
  .list-group-item { padding: 5px 15px; }
</style>
