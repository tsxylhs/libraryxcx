<template lang="pug">
  .w-100
    nav-bar(:title="'添加读书笔记'" :back-visible="true" :home-path="'/pages/index/main'")
    van-cell-group
      van-field(required clearable label="书名" placeholder="输入书名" v-model="notes.bookName" @change="handlebookname" :error-message="errMessage.name")
      van-field( clearable label="作者"  placeholder="请输入作者" v-model="notes.bookAuthor" @blur="handlebookAuthor" )
      van-field( clearable label="主题"  placeholder="请输入主题" v-model="notes.title" @change="handleTheme")
      van-field(clearable label="内容"   v-model="notes.desc" @change="handleDesc"  type='textarea', placeholder='请输入内容', autosize='true', border='')
    .mt-20p.w-90.m-auto
      van-button(size="large" round custom-class="btn-black" @click="onSave") 保存

</template>

<script>
  import API from '@/api/api'
  import NavBar from '@/components/NavBar.vue'
  import AreaList from '@/utils/area'

  export default {
    name: 'InfForm',
    components: {
      NavBar
    },
    data () {
      return {
        user: {},
        notes: {},
        userId: '',
        errMessage: {
          name: '',
          mobile: ''
        },
        areaList: AreaList,
        show: false

      }
    },
    methods: {
      handlebookname (event) {
        this.notes.bookName = event.mp.detail
      },
      handlebookAuthor (event) {
        this.notes.bookAuthor = event.mp.detail
      },
      handleTheme (event) {
        this.notes.title = event.mp.detail
      },
      handleDesc (event) {
        this.notes.desc = event.mp.detail
      },
      getBook (id) {
        API.books.get(id).then((res) => {
          debugger
          this.notes.bookName = res.name
          this.notes.bookAuthor = res.author
        }).catch((err) => {
          console.log(err)
        })
      },
      handleEmail (event) {
        this.user.email = event.mp.detail
      },
      loadUser (id) {
        API.user.get(id).then((res) => {
          this.user = res
          console.log('this.user', this.user)
        }).catch(() => {
        })
      },
      onSave () {
        API.notes.create(this.notes).then((res) => {
          console.log('success')
        }).catch(() => {
          console.log('error')
        })
      }
    },
    mounted () {
      if (this.$root.$mp.query.bookId) {
        this.getBook(this.$root.$mp.query.bookId)
      } else {
      }
    },
    onShow () {
      if (this.$root.$mp.query.bookId) {
        this.getBook(this.$root.$mp.query.bookId)
      } else {
      }
    }
  }
</script>

<style scoped>

</style>
