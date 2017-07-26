<template>
  <div class="post">
    <div class="loading" v-if="loading">Loading...</div>
    <div v-if="error" class="error">
      {{ error }}
    </div>
    <transition name="slide">
      <!--
        giving the post container a unique key triggers transitions
        when the post id changes.
      -->
      <div v-if="post" class="content" :key="post.id">
        <h2>{{ post.title }}</h2>
        <p>{{ post.body }}</p>
      </div>
    </transition>
  </div>
</template>

<script>
import { getPost } from './api'

export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 一般情况获取动态数据一般放在初始化created
    // created 这个钩子在实例被创建之后被调用
    this.fetchData()
  },
  watch: {
    // route 变化时，对应回调函数重新调用
    // Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个属性
    // 注意，不应该使用箭头函数来定义 watcher 函数
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
</script>

<style>
.loading {
  position: absolute;
  top: 10px;
  right: 10px;
}
.error {
  color: red;
}
.content {
  transition: all .35s ease;
  position: absolute;
}
.slide-enter {
  opacity: 0;
  transform: translate(30px, 0);
}
.slide-leave-active {
  opacity: 0;
  transform: translate(-30px, 0);
}
</style>
