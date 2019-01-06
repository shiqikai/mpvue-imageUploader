<template>
  <div class="container" @click="clickHandle('test click', $event)">

    <div class="userinfo" @click="bindViewTap">
      <img class="userinfo-avatar" v-if="userInfo.avatarUrl" :src="userInfo.avatarUrl" background-size="cover" />
      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div>


    <button class="counter" @click="uploadImage">点我上传图片</button>

  </div>
</template>

<script>
import card from '@/components/card'
import { formatTime } from '@/utils/index'
import uploadImage from '../../utils/upload/uploadFile'
export default {
  data () {
    return {
      motto: 'Hello World',
      userInfo: {}
    }
  },

  components: {
    card
  },

  methods: {
    bindViewTap () {
      const url = '../logs/main'
      wx.navigateTo({ url })
    },
    getUserInfo () {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: (res) => {
              this.userInfo = res.userInfo
            }
          })
        }
      })
    },
    clickHandle (msg, ev) {
      console.log('clickHandle:', msg, ev)
    },
    // 选择图片
    uploadImage () {
      wx.chooseImage({
        count: 9, // 默认最多一次选择9张图
        sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
        success: function (res) {
          // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
          var nowTime = formatTime(new Date())
          // 支持多图上传
          for (var i = 0; i < res.tempFilePaths.length; i++) {
            // 显示消息提示框
            wx.showLoading({
              title: '上传中' + (i + 1) + '/' + res.tempFilePaths.length,
              mask: true
            })
            // 你的域名下的/cbb文件下的/当前年月日文件下的/图片.png
            // 图片路径可自行修改
            uploadImage(res.tempFilePaths[i], 'cbb/' + nowTime + '/',
              function (result) {
                console.log('======上传成功图片地址为：', result)
                wx.hideLoading()
              }, function (result) {
                console.log('======上传失败======', result)
                wx.hideLoading()
              }
            )
          }
        }
      })
    }
  },

  created () {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo()
  }
}
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

.usermotto {
  margin-top: 150px;
}

.form-control {
  display: block;
  padding: 0 12px;
  margin-bottom: 5px;
  border: 1px solid #ccc;
}

.counter {
  display: inline-block;
  margin: 10px auto;
  padding: 5px 10px;
  color: blue;
  border: 1px solid blue;
}
</style>
