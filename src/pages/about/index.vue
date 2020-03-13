<template lang="pug">
  .w-100
    nav-bar(:title="'关于ElB'" :backVisible="true" :home-path="'/pages/index/main'")
    .p-20p
      .text-indent ElB是一个品牌化的生态B2B平台，链接全供应链渠道、最终实现产地到门店的无缝衔接。
</template>

<script>
  import NavBar from '@/components/NavBar.vue'

  export default {
    components: {
      NavBar
    },
    data () {
      return {
      }
    },
    methods: {
    },
    mounted () {
    }
  }
</script>

<style >
</style>
