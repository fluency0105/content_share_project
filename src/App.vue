<template>
  <el-container id="app">
    <el-header class="title">
      {{ dynamicValidateForm.title }}
    </el-header>
    <el-main>
      <div>
        <el-divider><i class="el-icon-present"></i></el-divider>
        <p>
          {{ dynamicValidateForm.brief }}
        </p>
      </div>
      <div class="" v-for="(content,index) in dynamicValidateForm.contents" :key="index">
        <el-divider v-if="content.type == 'image'"><i class="el-icon-picture-outline-round"></i></el-divider>
        <el-divider v-if="content.type == 'video'"><i class="el-icon-video-camera-solid"></i></el-divider>
        <el-divider v-if="content.type == undefined || content.type == null || content.type == 'text'"></el-divider>
        <p>{{ content.contentBrief }}</p>
        <div v-for="(file,index) in content.fileList" :key="file.name">
          <el-image v-if="content.fileList[index].type.substring(0,5) === 'image'"
            :src="file.url"
            >
          </el-image>
          <video v-if="content.fileList[index].type.substring(0,5) === 'video'"
            class="video"
            rel="preload"
            controls="controls">
            <source :src="file.url" type="video/mp4">
            您的浏览器不支持视频播放
          </video>
        </div>
      </div>
      <el-divider v-if="dynamicValidateForm.displayComment"><i class="el-icon-chat-line-round"></i></el-divider>
      <div v-if="dynamicValidateForm.displayComment">
        <p>
          {{ dynamicValidateForm.comment }}
        </p>
      </div>
      <el-divider v-if="dynamicValidateForm.displayFeedback"><i class="el-icon-mic"></i></el-divider>
      <el-form v-if="dynamicValidateForm.displayFeedback" ref="form" :model="form" :label-position="labelPosition">
        <p>
          您会向身边的朋友推荐我们吗？<br>
          5星为非常愿意推荐，1星为不会推荐。
        </p>
        <el-rate class="rate"
          v-model="rateData.rateValue"
          :colors="rateData.colors">
        </el-rate>
        <el-button round type="success" @click="onSubmit" icon="el-icon-share">分享</el-button>
        <el-form-item class="feedback" label="非常希望能听到您的心声,为我们指明改进的方向">
          <el-input type="textarea" v-model="form.desc"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button round type="primary" @click="onSubmit">提交</el-button>
        </el-form-item>
      </el-form>
    </el-main>
    <el-footer>
      <HelloWorld msg="关于我们"/>
    </el-footer>
  </el-container>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data () {
    return {
      dynamicValidateForm: {
        contents: [{
          type: '',
          contentBrief: '',
          fileList: []
        }],
        title: '',
        brief: '',
        displayComment: false,
        comment: '',
        displayFeedback: true
      },
      rateData: {
        rateValue: null,
        colors: ['#bedbfd', '#edef4d', '#FF9900']
      },
      labelPosition: 'top',
      imagePreviewList: [
        ['https://fuss10.elemecdn.com/1/8e/aeffeb4de74e2fde4bd74fc7b4486jpeg.jpeg'],
        ['https://fuss10.elemecdn.com/8/27/f01c15bb73e1ef3793e64e6b7bbccjpeg.jpeg']
      ],
      form: {
        rate: '',
        desc: ''
      }
    }
  },
  mounted: function () {
    this.$http({
      method: 'get',
      url: 'http://localhost:9001/article/getArticle',
      params: {
        id: this.getQueryVariable('id')
      }
    }).then(function (response) {
      this.dynamicValidateForm.title = response.data.data.title
      this.dynamicValidateForm.brief = response.data.data.brief
      this.dynamicValidateForm.contents = JSON.parse(response.data.data.contentsJSON)
      this.dynamicValidateForm.displayComment = response.data.data.displayComment
      if (this.dynamicValidateForm.displayComment) {
        this.dynamicValidateForm.comment = response.data.data.comment
      }
      this.dynamicValidateForm.displayFeedback = response.data.data.displayFeedback
    }.bind(this)).catch(function (error) {
      console.log(error)
    })
  },
  methods: {
    onSubmit () {
      this.$notify({
        title: '谢谢您的反馈',
        message: '',
        type: 'success'
      })
    },
    getQueryVariable (variable) {
      var query = window.location.search.substring(1)
      var vars = query.split('&')
      for (var i = 0; i < vars.length; i++) {
        var pair = vars[i].split('=')
        if (pair[0] === variable) {
          return pair[1]
        }
      }
      return (false)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.title {
  font-size: 20px;
}
.video {
  width:100%;
  max-height:250px;
}
.rate {
  margin-top: 24px;
  margin-bottom: 28px;
}
.feedback {
  margin-top: 30px;
}
</style>
