<template>
  <div class="app">
    <h1>Page Posts</h1>
    <my-input
        v-model="searchQuery"
        placeholder="Search...."
    />
    <div class="app__btns">
      <my-button @click="showDialog">
        Create post
      </my-button>
      <my-select
          v-model="selectedSort"
          :options="sortOptions"
      />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form @create="createPost"/>
    </my-dialog>
    <post-form @create="createPost"/>
    <post-list
        :posts="sortedAndSearchedPosts"
        @remove="removePost"
        v-if="!isPostsLoading"/>
    <div v-else>Идет загрузка...</div>
    <div class="page__wrapper">
      <div
          v-for="pageNumber in totalPages"
          :key="pageNumber"
          class="page"
          :class="{
            'current-page': page === pageNumber
          }"
          @click="changePage(pageNumber)"
      >
        {{ pageNumber }}
      </div>
    </div>
  </div>
</template>

<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import MyButton from "@/components/UI/MyButton";
import axios from 'axios';
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";

export default {
  components: {
    MyButton, MySelect, MyInput,
    PostForm, PostList
  },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: '',
      searchQuery: '',
      totalPages: 0,
      page: 1,
      limit: 10,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'},
      ]
    }
  }, methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(p => p.id !== post.id)
    },
    showDialog() {
      this.dialogVisible = true;
    },
    changePage(pageNumber) {
      this.page = pageNumber;
    },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit,
          }
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit);
        this.posts = response.data;
        this.isPostsLoading = false;
      } catch (e) {
        alert('Error');
      }
    },
  },
  mounted() {
    this.fetchPosts();
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    },
  },
  watch: {
    page(){
      this.fetchPosts();
    }
    /*selectedSort(newValue) {
      this.posts.sort((post1, post2) => {
        return post1[newValue]?.localeCompare(post2[newValue])
      })
    },*/
  }
}
</script>


<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  padding: 20px;
}

.app__btns {
  display: flex;
  justify-content: space-between;
  margin: 15px 0;
}

.page__wrapper {
  display: flex;
  margin-top: 15px;
}

.page {
  border: 1px solid black;
  padding: 10px;
}

.current-page {
  border: 2px solid teal;
}
</style>