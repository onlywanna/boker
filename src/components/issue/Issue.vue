<template>
  <div>
      <!-- 标题 -->
      <div>
        <h3 class="title">文章标题：</h3>
        <el-input v-model="title" placeholder="请输入内容"></el-input>
      </div>
      <!-- 分类和时间选择 -->
      <div class="mate">
        <el-row class="demo-autocomplete">
          <el-col :span="12">
            <h3 class="title">选择分类</h3>
            <el-autocomplete
              class="inline-input"
              v-model="classify"
              :fetch-suggestions="querySearch"
              placeholder="请输入内容"
              @select="handleSelect"
            ></el-autocomplete>
          </el-col>
          <el-col :span="12">
            <h3 class="title">选择发布日期</h3>
            <el-date-picker
              v-model="timer"
              type="date"
              placeholder="选择日期"
              value-format="yyyy-MM-dd"
            ></el-date-picker>
          </el-col>
        </el-row>
      </div>
      <!-- 富文本 -->
      <h3 class="title">文章内容：</h3>
      <div>
        <vue-html5-editor
          :content="content"
          :height="500"
          ref="editor"
          @change="updateData"
          :show-module-name="showModuleName"
        ></vue-html5-editor>
      </div>
      <!-- 提交 -->
      <div class="submit">
        <el-button type="primary" @click="submit">
          提交
          <i class="el-icon-upload el-icon--right"></i>
        </el-button>
      </div>
 
  </div>
</template>

<script>
export default {
  name: "Issue",
  data() {
    return {
      isclearable: true,
      restaurants: [],
      title: "",
      classify: "",
      timer: "",
      content: "",
      showModuleName: false,
      flag:true
    };
  },
  methods: {
   createFilter(queryString) {
        return (restaurant) => {
          return (restaurant.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0);
        };
      },
    querySearch(queryString, cb) {
      var restaurants = this.restaurants;
      var results = queryString
        ? restaurants.filter(this.createFilter(queryString))
        : restaurants;
      // 调用 callback 返回建议列表的数据
      cb(results);
    },
    loadAll() {
      return [
        { value: "css" },
        { value: "html" },
        { value: "js" },
        { value: "vue" }
      ];
    },
    handleSelect(item) {
      console.log(item);
    },
    submit() {
      // console.log(this.content, 555);
      var _this = this;

    //  this.content.replace(/\"/g,"lyhts")
    //  this.content.replace(/\'/g,"lyhtss")
    //  this.content.replace(/\'/g,"lyhtss")


      var postData = {
        id: Date.parse(new Date()),
        title: this.title,
        classify: this.classify, 
        bodytext:this.content.replace(/[\\"']/g, '\\$&'),
        timer: this.timer
      };
      // console.log(postData.bodytext);
      
      this.$http.post("/add", postData).then(function(res) {
        if (res.data.state) {
          _this.$notify({
            title: "成功",
            message: "添加成功",
            type: "success"
          });
           //添加成功重新查看数据
         _this.$http.get("/look").then(function(res) {_this.$store.commit("allData", res.data.data);});
          //添加成功跳回首页
          _this.$router.push({
            name: "Home"
          });
        } else {
          _this.$notify.error({
            title: "错误",
            message: "添加失败"
          });
        }
      });
    },
    updateData: function(data) {
      // sync content to component
      this.content = data;
    },
    open() {
        this.$prompt('请输入密码', '请输入管理员密码', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
           inputPattern: /[4-7][1-9]./,
           inputErrorMessage: '密码格式错误'
        }).then(({ value }) => {
          this.$notify({
            type: 'success',
            message:'登录成功',
             position: 'top-right'
          });
        }).catch(() => {
          this.$notify.error({
            message:'取消输入',
            position: 'top-right'
          });       
        });
      }
  },
  mounted() {
    this.restaurants = this.loadAll();
  }
};
</script>


<style scoped lang="less">
.title {
  padding: 15px 0;
}
.submit {
  margin: 20px auto;
  text-align: center;
}
#editor {
  height: 450px;
}
.logoing{
  display: block;
  width: 100%;
  height: 800px;
  // position: fixed;
  // top: 85px;
  // left: 0px;
  // background:url("/static/images/logo.jpg") no-repeat 100% 100%;
}
</style>
