<template lang="pug">
  .wrapper
    b-navbar(variant="info", type="dark")
      b-navbar-brand(tag="h1", :to="{ name: 'Homepage' }")
        .user-avatar(:style="avatarStyles(post.author.avatar)")
        small.pl-2 Blog
    b-row.entry
      b-container
        b-col(cols="12")
          h1.mb-4 {{ post.title }}
          hr
          article.entry-article(v-html="post.html")
          hr
          span Updated at {{ toNow(post.updated_at) }}.
</template>

<script>
import postsAPI from '@/api/posts'
import { distanceInWordsToNow, parse } from 'date-fns'

export default {
  name: 'entry',
  beforeRouteEnter (to, from, next) {
    postsAPI.fetchPostByAlias(to.params.alias)
      .then(res => {
        next(vm => {
          vm.post = res.results
        })
      }, () => {
        next(vm => {
          vm.postsIsEmpty = true
        })
      })
  },
  methods: {
    truncate (string) {
      return string.substring(0, 120)
    },
    toNow (date) {
      return distanceInWordsToNow(parse(date), { addSuffix: true })
    },
    avatarStyles (avatar) {
      return {
        backgroundImage: 'url(' + avatar + ')'
      }
    }
  },
  data () {
    return {
      postsIsEmpty: false,
      post: {
        title: '',
        html: '',
        author: {
          name: '',
          avatar: ''
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.user-avatar {
  display: inline-block;
  width: 30px;
  height: 30px;
  background: {
    position: 50% 50%;
    repeat: no-repeat;
    color: #666;
    size: cover;
  }
  border-radius: 50%;
  vertical-align: middle;
}
.entry-article {
  /deep/ {
    img {
      max-width: 100%;
      display: block;
    }
  }
}
</style>
