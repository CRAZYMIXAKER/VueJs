<template>
  <form @submit.prevent>
    <h4>Create post</h4>
    <my-input
        v-model="post.title"
        placeholder="Название"/>
    <my-input
        v-model="post.body"
        placeholder="Описание"/>
    <my-button
        @click="createPost"
        style="align-self: flex-end;   margin-top: 15px;">
      Create
    </my-button>
  </form>
</template>

<script>

export default {
  data() {
    return {
      post: {
        title: '',
        body: '',
      }
    }
  },
  methods: {
    createPost() {
      this.post.id = Date.now();
      this.$emit('create', this.post, 'second param', '3 param')
      this.post = {
        title: '',
        body: '',
      }
    },
  },
}
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
}
</style>