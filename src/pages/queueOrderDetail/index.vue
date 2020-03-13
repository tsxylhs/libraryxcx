<template lang="pug">
  .w-100
    nav-bar(:title="'排队订单'" :backVisible="true" :home-path="'/pages/index/main'")
    .p-20p
      .bg-white.pt-10p.shadow.borRadius-5.border.mt-10p
        .text-center.p-10p(style="border-bottom: 1px dashed #ECEEF2")
          .pf-subhead {{queue.shop.name}}
          .mt-10p.text-black 您的排队号码
          .mt-10p.pf-title {{queue.queueNo}}
        .p-20p.df-row-jb.border-bottom(v-if="queue.queueProcess === 'waiting'")
          .flex-1.border-right.df-col-ac
            .text-red.pf-title {{queue.waitingCount}}
            .mt-10p 前方等待人数
          .flex-1.df-col-ac
            .text-red.pf-title {{queue.waitingTime}}分钟
            .mt-10p 预计等待时长
        .p-20p.border-bottom.text-center(v-else)
          .text-red.pf-title(v-if="queue.queueProcess === 'cancel'") 已取消
          .text-red.pf-title(v-if="queue.queueProcess === 'finished'") 商家已叫号
          .text-red.pf-title(v-if="queue.queueProcess === 'failed'")
            div 排队失败
            div 非常抱歉，商家无法提供该服务
        .bg-dark.p-20p.borRadius-b
          .text-black {{queue.shop.name}}
          .mt-10p.d-flex
            div
              .location.mr-5p
            span {{queue.shop.region + queue.shop.address}}
          .mt-10p.df-row-ac
            .circle-phone.mr-5p
            span 电话：{{queue.shop.phoneNumber}}
        //.my-20p.w-50.m-auto
          van-button.w-50(size="large" round custom-class="btn-minEdit" @click="handleCancel(q)" v-if="q.queueProcess === 'waiting'") 取消排队
          van-button(size="large" round custom-class="btn-minEdit" @click="resubmit(q)" v-if="q.queueProcess === 'cancel'") 重新排队取号
      .mt-20p.text-center 听到叫号请到前台，过号请重新取号
      .mt-30p.text-center.pf-subhead(@click="toOrder") 查看全部排队>
      van-popup(:show='showPop', position='center', overlay='false', @close='onClose' safe-area-inset-top="true" custom-style="width: 70% !important")
        .borRadius-30
          .p-20p
            .df-row-ac-jb
              .pf-subhead
              .cancel(@click='onClose')
            .mt-10p.text-center.pf-subhead.py-20p 请确认您是否要取消排队?
          .df-row-ac
            .flex-1.text-red.px-20p.py-10p.text-center(@click='onClose') 取消
            .flex-1.bg-red.px-20p.py-10p.text-white.text-center(@click='cancel') 确认

</template>

<script>
  import NavBar from '@/components/NavBar.vue'
  import API from '@/api/api'

  export default {
    components: {
      NavBar
    },
    data () {
      return {
        queues: [],
        queue: {},
        showPop: false,
        order: {},
        queueSuccess: false
      }
    },
    methods: {
      toShopDetail () {
        wx.navigateTo({
          url: '/pages/shopDetail/main'
        })
      },
      toOrder () {
        wx.switchTab({
          url: '/pages/order/main'
        })
      },
      handleCancel (order) {
        this.order = order
        this.showPop = true
      },
      onClose () {
        this.showPop = false
      },
      cancel () {
        this.showPop = false
        this.order.queueProcess = 'cancel'
        API.order.update(this.order).then(res => {
          console.log('res', res)
          this.viewMyQueue()
        })
      },
      resubmit (order) {
        // debugger
        order.queueProcess = 'waiting'
        API.order.restart(order).then(res => {
          console.log('res', res)
          this.viewMyQueue()
        }).catch(() => {

        })
      },
      viewMyQueue (val) {
        let phoneNumber = wx.getStorageSync('phoneNumber')
        API.order.queue({phoneNumber: phoneNumber, IsQueue: true, shopId: val || '0'}).then(res => {
          console.log('我的所有预约', res)
          this.queues = res.data
        })
      }
    },
    onShow () {
      let orderId = this.$mp.query.orderId
      if (orderId) {
        API.order.getQueue(orderId).then(res => {
          this.queue = res
          console.log('--------', res)
        })
      } else {
        //
      }
    }
  }
</script>

<style >
</style>
