<template>
  <div class="page_content">
    <div class="logo" v-show="showLogo && !$root.showIdentityInput">
      <div class="logo_img"></div>
      <p>(。・`ω´・) E8全体小伙伴欢迎新同学到来</p>
    </div>
    <div class="input_box">
      <div class="input_container">
        <input id="student_name" :class="{'error': inputErr}" type="text"  v-model="studentName"  class="student_input" placeholder="请输入你的姓名">
        <button @click="login" class="login_button">登陆</button>
      </div>
      <div class="tip">
        <ul>
          <li @click="selStudentName(item)" v-for="item in matchList">{{item}}</li>
        </ul>
      </div>
    </div>
    <t-footer v-if="showFooter" :position="true"></t-footer>
  </div>
</template>

<script>
import store from './store'
import TFooter from './components/TFooter'
export default {
  components: {
    TFooter
  },
  data () {
    return {
      // 是否显示logo
      showLogo: true,
      // 姓名
      studentName: '',
      // 输入框是否显示logo
      inputErr: false,
      // 是否显示底部
      showFooter: true,
      // 匹配列表
      matchList: [],
      isSel: false
    }
  },
  ready () {
    /* store.getMe(this, true).then(res => {
      if (res.student.is_report === 1 && res.student.whether_has_dorm === 1) {
        // 直接跳转到最终页面
        this.$route.router.go('final')
      } else if (res.student.is_report === 1) {
        // 跳转到登记成功页面
        this.$route.router.go('reportok')
      }
    }) */
    let studentIdInput = this.$el.querySelector('#student_name')
    studentIdInput.onfocus = () => {
      this.isSel = false
      this.showLogo = false
      this.showFooter = false
    }
    studentIdInput.onblur = () => {
      setTimeout(() => {
        this.showFooter = true
        this.showLogo = true
      }, 500)
    }
  },
  watch: {
    'studentName' () {
      if (!this.isSel) {
        if (this.studentName.length > 0) {
          store.searchStudents(this, this.studentName).then(res => {
            this.matchList = res
          })
        } else {
          this.matchList = []
        }
      }
    }
  },
  methods: {
    // 选择姓名
    selStudentName (studentName) {
      this.isSel = true
      this.studentName = studentName
      this.matchList = []
    },
    login () {
      this.$root.studentName = this.studentName
      setTimeout(() => {
        this.$root.showIdentityInput = true
        this.$root.IdentityNums = []
      }, 500)
      this.$el.querySelector('#student_name').blur()
    },
    // 替换匹配列表文字 add <em> tag
    replaceListTest (listItem) {
      if (this.matchList.search.length > 0) {
        return listItem.replace(this.matchList.search, '<span sytle="font-weight: normal;">' + this.matchList.search + '</span>')
      } else {
        return listItem
      }
    }
  }
}
</script>

<style lang="less" scoped>
  .logo{
    margin-top: 1rem;
    margin-bottom: .5rem;
    >.logo_img{
      width: 12.6rem;
      height: 3.5rem;
      padding-top: .8rem;
      background: url('./assets/logo.png') no-repeat 50% 50%;
      background-size: 100% 100%;
      margin: 0 auto;
    }
    >p{
      font-size: .6rem;
      text-align: center;
      margin-top: .6rem;
      color: #aaa;
    }
  }
  .input_box{
    padding: 1rem;
    margin-top: 1.5rem;
    >.tip{
      border-top: 0;
      border-radius: 0 0 2px 2px;
      background: #FFF;
      -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.2);
      box-shadow: 0 1px 3px rgba(0,0,0,0.2);
      margin: 1px;
      margin-top: 0;
      >ul{
        font-size: 16px;
        line-height: 1.5rem;
        text-align: left;
        color: #333;
        >li{
          padding-left: .5rem;
          transition: background-color .3s;
          &:active{
            background-color: #eef3fe;
          }
        }
      }
    }
    >.input_container{
      padding-right: 3.3rem;
      position: relative;
      >.student_input{
        background-color: #fff;
        height: 2.2rem;
        width: 100%;
        border-radius: 0.1rem 0 0 0.1rem;
        padding: 0 0.5rem;
        border: 1px solid #eee;
        outline: 0;
        transition: border-color .3s, box-shadow .3s;
        font-size: .75rem;
        color: #333;
        &:focus{
          border: 1px solid #82c3fe;
          box-shadow: inset 0 1px 1px rgba(0,0,0,0.1);
        }
        &.error{
          border-color: #f30;
        }
      }
      .login_button{
        position: absolute;
        right: 0px;
        top: 0;
        height: 100%;
        width: 3.3rem;
        background: #38f;
        color: #fff;
        font-size: .75rem;
        border: 1px #eee solid;
        white-space: nowrap;
        letter-spacing: -1px;
        box-sizing: inherit;
        border-left: none;
      }
    }
  }
</style>
