<template lang="html">
  <div>
    <form @submit="submitForm" v-show="mode != 'clear'">
      <div class="form-group">
        <label for="title">Title</label>
        <textarea rows="2"
                  v-model="displayPost.title"
                  :readonly="mode == 'view'"
                  required
                  class="form-control" id="title"></textarea>
      </div>
      <div class="form-group">
        <label for="body">Post Body</label>
        <textarea rows="8"
                  v-model="displayPost.body"
                  :readonly="mode == 'view'"
                  required
                  class="form-control" id="body"></textarea>
      </div>

      <div v-if="mode == 'view'" class="float-right">
        <button @click="editPost" type="button" class="btn btn-secondary">Edit Post</button>
        <button @click="deletePost" type="button" class="btn btn-danger">Delete Post</button>
      </div>
      <div v-else class="float-right">
        <button @click="cancelEdit" type="button" class="btn btn-secondary">Cancel</button>
        <button type="submit" class="btn btn-primary">Save Post</button>
      </div>
    </form>
  </div>
</template>

<script>
  import { eventBus } from '../main.js';
  export default {
    props: {
      base_url: {type: String, required: true},
      userId: {type: Number, required: true}
    },
    data: function() {
      return {
        post: {},
        displayPost: {},
        mode: 'clear',
      };
    },
    methods: {
      cancelEdit() {
        this.displayPost = {};
        this.mode = 'clear';
      },
      submitForm(e) {
        // submit allows form validation, but this prevents submit page reload
        e.preventDefault();

        if (this.mode == 'new') {
          this.saveNewPost();
        } else if (this.mode == 'edit') {
          this.savePost();
        }
      },
      viewPost(post) {
        this.post = post;
        this.displayPost = Object.assign({}, this.post);  // make copy of object
        this.mode = 'view';
      },
      editPost() {
        this.mode = 'edit';
      },
      savePost() {
        // update post via api PUT
        var url = this.base_url + '/' + this.displayPost.id;
        this.$http.put(url, this.displayPost)
            .then(response => {
              return response.json();
            }, error => {
              console.log(error);
              alert('Server Update Error (alert used in lieu of flash-type messaging): ' + error.statusText);
            })
            .then(data => {
              if (data) {
                // update local state with api results, via object ref
                this.post.title = data.title;
                this.post.body = data.body;
                this.mode = 'clear';
              };
            });
      },
      editNewPost() {
        this.displayPost = {id: null, title: '', body: '', userId: this.userId};
        this.mode = 'new';
      },
      saveNewPost() {
        // create new post via api POST
        this.$http.post(this.base_url, this.displayPost)
            .then(response => {
              return response.json();
            }, error => {
              console.log(error);
              alert('Server Create Error (alert used in lieu of flash-type messaging): ' + error.statusText);
            })
            .then(data => {
              if (data) {
                eventBus.$emit('saveNewPost', data);
                this.mode = 'clear';
              }
            });
      },
      deletePost() {
        // delete post via api DELETE
        var url = this.base_url + '/' + this.post.id;
        this.$http.delete(url)
            .then(response => {
              return response.status;
            }, error => {
              console.log(error);
              alert('Server Delete Error (alert used in lieu of flash-type messaging): ' + error.statusText);
            })
            .then(status => {
              if (status === 200) {
                eventBus.$emit('deletePost', this.post);
                this.mode = 'clear';
              }
            });
      },
    },
    created() {
      eventBus.$on('postSelected', post => {
        this.viewPost(post);
      });

      eventBus.$on('newPost', data => {
        this.editNewPost();
      });
    }
  }
</script>

<style scoped lang="css">
</style>
