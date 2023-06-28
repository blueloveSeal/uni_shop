<template>
  <view class="content">
    <view class="data">
      <text>{{val}}</text>
    </view>
    <button type="primary" size="mini" @click="communicatonOff">结束</button>
  </view>
</template>

<script>
  export default {
    data () {
      return {
        val: 0
      }
    },
    onLoad() {
      setInterval(()=>{
        uni.$emit('add',{
          data: 2
        })
      },100)
      uni.$on('add', this.add)
    },
    methods: {
      communicatonOff () {
        uni.$off('add', this.add)
      },
      add (e) {
        this.val+=e.data
      }
    }
  }
</script>