<template lang="pug">
  .w-100(style="min-height: 100%")
    nav-bar(:title="'添加读书笔记'" :backVisible="true" :home-path="'/pages/index/main'")
    .p-10p
      .pf-title 请填写以下读书信息
        .px-10p.pt-20p.text-3(style="font-size: 14px") 书名
        van-field(required clearable placeholder="输入书名名称" v-model="domain.name" @blur="handleName" :error-message="errMessage.name")
        .px-10p.pt-20p.text-3(style="font-size: 14px") 作者
        van-field(required clearable  placeholder="请输入作者" v-model="domain.author" @blur="handleauthor")
        .px-10p.pt-20p.text-3(style="font-size: 14px") 内容
        van-field(required clearable  placeholder="请输入内容"  v-model="domain.text" @blur="handleText"   type='textarea')

</template>

<script>
  import NavBar from '@/components/NavBar.vue'
  import Data from '@/utils/data'
  import API from '@/api/api'
  import {loginInfo} from '../../utils/login'
  import Moment from 'moment'

  export default {
    components: {NavBar},
    data () {
      return {
        isLogged: false,
        active: 0,
        data: Data,
        myOrders: [],
        epOrder: true
      }
    },
    onShareAppMessage (object) {
      object.title = '在线ELB'
      object.path = '/pages/index/main'
      object.imageUrl = 'https://mtms.letsit.vip/share.jpg'
      return object
    },
    watch: {},
    methods: {
      checkUser (e) {
        loginInfo(e, this, this.loadNotes)
      },
      loadNotes (options) {
        let user = wx.getStorageSync('user')
        let params = {ps: 20, userId: user.id}
        params = Object.assign(params, options)
        API.order.list(params).then(res => {
          this.isLogged = true
          if (res.data === undefined || res.data.length <= 0) {
            this.epOrder = true
            console.log('没有订单')
          } else {
            this.myOrders = res.data
            res.data.forEach(order => {
              if (order.createdAt && order.isQueue) {
                order.createdAt = Moment(order.createdAt).format('YYYY-MM-DD HH:mm')
              }
            })
            console.log('有订单')
            this.epOrder = false
          }
        }).catch((err) => {
          console.log('err', err)
        })
      }
    },
    mounted () {
      // let user = wx.getStorageSync('user')
      // if (user) {
      // this.loadOrders()
      // }
    },
    onShow () {
      let bookId = this.$mp.query.bookId
      if (bookId) {
        console.log(bookId)
      } else {
        console.log('new')
      }
    }
  }
</script>

<style scoped>
</style>
