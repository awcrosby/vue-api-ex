<template lang="html">
  <div>
    <header class="row">
      <div class="col">
        <h3>My blog posts</h3>
      </div>
    </header>

    <div class="row" id="new-post">
      <div class="col">
        <button @click="newPost" type="button" class="btn btn-secondary">Create New Post</button>
      </div>
    </div>

    <div class="row">
      <div class="col-sm-6">
        <ul class="list-group">
          <app-post-item v-for='post in posts' :key='post.id' :post='post'></app-post-item>
        </ul>
      </div>
      <div class="col">
        <app-post-detail :userId='userId' :base_url='base_url'></app-post-detail>
      </div>
    </div>
  </div>
</template>

<script>
  import PostItem from './PostItem.vue';
  import PostDetail from './PostDetail.vue';
  import { eventBus } from '../main.js';

  export default {
    data: function() {
      return {
        posts: [],
        userId: 1,  // placeholder for what auth session would provide
        base_url: 'https://jsonplaceholder.typicode.com/posts'
      };
    },
    components: {
      'app-post-item': PostItem,
      'app-post-detail': PostDetail
    },
    methods: {
      fetchData() {
        this.$http.get(this.base_url + '?userId=' + this.userId)
            .then(response => {
              return response.json();
            }, error => {
              console.log(error);
            })
            .then(data => {
              this.posts = data;
            });
      },
      newPost() {
        eventBus.$emit('newPost', 'test_data_string');
      }
    },
    created() {
      this.fetchData();

      eventBus.$on('saveNewPost', post => {
        this.posts.push(post);
      });

      eventBus.$on('deletePost', post => {
        var index = this.posts.map(x => x.id).indexOf(post.id);
        this.posts.splice(index, 1);
      });
    }
  }
</script>

<style scoped lang="css">
  header { padding: 20px 0 20px 0; }
  #new-post { padding: 0 0 20px 0; }
</style>
