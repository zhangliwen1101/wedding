<template>
    <div class="section">
        <video id="myVideo" src="https://vodkgeyttp9.vod.126.net/vodkgeyttp8/Yzye7Vfe_735069_hd.mp4?wsSecret=19b7c3db3f411c9f637a65c4af411d8c&wsTime=1552232105&ext=jC%2Fk7q%2BCq%2FO1S1X%2FP9welz2kSrj23N3v%2FzvTEuux1PcL%2FH1Teg3fxwFiQVk9SUHVU2wOyV5pyBD1LGHskMbP2KuQet%2FGH3mQuSVE1BYrhbKsNe8p2LXoD%2FxxW7qJnnxwwgHPtxcffbihVRKrJ1y9spoYAOS775yXnQ2cR%2BoaDhf2HnyUCX91fZFkMZ%2F%2B127fTCKJiW6sfw1lAQlDPhIQwGoaJ%2FyKdfkKlBAxrr%2BEv2mQjcJcneZSkTLdHm5Sy7h%2F6XnPGZGsDSIEDfa0q4wa3OgqljDZdvQrWVPYyQnBgZGuPekEVZwiYg3Wek63DDuTE4z%2F3FPs09wCuHu%2Fw9mG7P83PaOQkxD7qHS4L%2BdwkHJ3hg1vWCSCs0WFXQOHxZg5KAsucEAFWoxAwvVxfcBtErgB73JdoHmRzQ1%2BUpHND5GJEQ2d0msYfXvAWeuJnWMCsl0bf%2B%2FvDk2YQE%2FWm2TEodQTuXCWnrV4wlqRJsX6r5hpcPv52l%2Bjy%2B7yMd1nG3JLTacwREBvcLYOuA3HKAddLMrkNXze6V%2BggOLtZwAtsyU%3D" :danmu-list="danmuList" enable-danmu danmu-btn controls poster="http://img.jammyfm.com/wordpress/wp-content/uploads/2017/01/201701221330291592-720x480.jpg?x-oss-process=image/resize,w_720,h_480,m_fill,limit_0"></video>
        <div class="btn-area">
            <input @blur="bindInputBlur" placeholder="请输入临时弹幕，真实弹幕来自留言" placeholder-style="color:#bbb"/>
            <button @tap="bindSendDanmu">点击生成临时弹幕</button>
            <image src="../../static/images/close1.png" @tap="close"/>
        </div>
    </div>
</template>

<script>
export default {
  name: 'Video',
  data () {
    return {
      inputValue: '',
      danmuList: []
    }
  },

  onLoad () {
    const that = this
    that.getMessageList()
    that.videoContext = wx.createVideoContext('myVideo')
  },

  methods: {
    bindInputBlur (e) {
      let that = this
      that.inputValue = e.mp.detail.value
    },

    bindSendDanmu: function () {
      let that = this
      that.videoContext.sendDanmu({
        text: that.inputValue,
        color: that.getRandomColor()
      })
    },

    getRandomColor () {
      let rgb = []
      for (let i = 0; i < 3; ++i) {
        let color = Math.floor(Math.random() * 256).toString(16)
        color = color.length === 1 ? '0' + color : color
        rgb.push(color)
      }
      return '#' + rgb.join('')
    },

    getMessageList () {
      const that = this
      const db = wx.cloud.database()
      const message = db.collection('msg')
      message.get().then(res => {
        let data = res.data.reverse()
        let arr = []
        if (data.length > 0) {
          data.forEach((item, index) => {
            arr.push({
              text: item.desc,
              color: that.getRandomColor(),
              time: 1 + (index * 2)
            })
          })
          that.danmuList = arr
        }
      })
    },

    close () {
      const that = this
      that.$emit('closeVideo')
    }
  }
}
</script>

<style lang="stylus" scoped>
.section
    width 750rpx
    position relative
    #myVideo
        height 422rpx
        width 100%
        margin-bottom 20rpx
    .btn-area
        input
            width 660rpx
            border 1rpx solid #e5e5e5
            padding-left 30rpx
            height 80rpx
            line-height 80rpx
            font-size 30rpx
            color #ccc
            margin-bottom 30rpx
            margin-left 30rpx
            border-radius 16rpx
        button
            height 92rpx
            width 690rpx
            border-radius 16rpx
            background #1AAD19
            color #fff
            line-height 92rpx
            text-align center
            font-size 36rpx
            margin-bottom 40rpx
        image
            width 80rpx
            height 80rpx
            background #fff
            margin 0 auto
</style>
