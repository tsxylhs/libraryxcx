<template lang="pug">
  .w-100
    nav-bar(:title="'读书笔记'" )
    .w-100(v-if="isLogged")
      van-tabs.order(:active="active" @change="changeTab" :border="false" color="#FF7012" line-width="30" z-index="0")
        van-tab(title="全部")
        van-tab(title="待付款")
        van-tab(title="待发货")
        van-tab(title="待收货")
        van-tab(title="已完成")
      .df-col-ac-jc.text-dark(v-if="epOrder" style="margin-top: 200rpx;")
        .ep-order
        .mt-10p.mr-20p 还没有读书笔记
    .w-100.mt-50p(v-else)
      .df-col-ac.p-20p
        .login-none
        .mt-10p 请先登录，以查看读书笔记。
        button.btn-main.mt-10p(v-if="!user" open-type="getUserInfo" @getuserinfo="checkUser" lang="zh_CN" type="primary" round @click="checkUser") 微信授权登录


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
    watch: {
    },
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
      let user = wx.getStorageSync('user')
      if (user) {
        this.isLogged = true
      }
    }
  }
</script>

<style scoped>
</style>
