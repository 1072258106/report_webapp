<template>
  <div @click="showLogo=true" class="page_content">
    <div class="logo" v-show="showLogo && !$root.showIdentityInput">
      <div class="logo_img"></div>
      <p class="title">2016计算机学院新生自助报到</p>
      <p>(。・`ω´・) E8全体小伙伴欢迎新同学到来</p>
    </div>
    <div class="input_box">
      <div class="placeholder" v-show="!showLogo">支持简拼搜索 如:小明(xm)</div>
      <div class="input_container">
        <input @click.stop id="student_name" :class="{'error': inputErr}" type="text"  v-model="studentName"  class="student_input" placeholder="请输入你的姓名">
        <button @click.stop="login" class="login_button">登录</button>
      </div>
      <div class="tip">
        <ul>
          <li @click.stop="selStudentName(item)" v-for="item in matchList"><button>{{item}}</button></li>
        </ul>
      </div>
    </div>
    <t-footer :position="true"></t-footer>
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
      // 匹配列表
      matchList: [],
      isSel: false,
      // 输入框是否是焦点
      isFocus: false
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
    if (window.localStorage.isReadTip === undefined) {
      this.$root.showAlert('目前江湖险恶，骗子横行，学校或者其他教育部门不会要求学生将学费或其他费用转存到其他账户;学校不会向学生推销任何产品。如有疑问，请事先向学校咨询(校保卫处:0554-6862110)。安全警钟时刻长鸣。', '重要提醒', '朕已阅')
      window.localStorage.isReadTip = '1'
    }
    let studentIdInput = this.$el.querySelector('#student_name')
    studentIdInput.onfocus = () => {
      this.isSel = false
      this.showLogo = false
      this.isFocus = true
    }
    studentIdInput.onblur = (event) => {
      if (event.relatedTarget === null) {
        this.showLogo = true
        setTimeout(() => {
          this.isFocus = false
        }, 500)
      }
    }
    studentIdInput.onkeydown = (event) => {
      if (event.keyCode >= 48 && event.keyCode <= 57) {
        return false
      }
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
      this.login()
    },
    login () {
      if (this.studentName.length === 0) {
        return false
      }
      store.studentNameIsExit(this.studentName, this).then(res => {
        this.$root.studentName = this.studentName
        if (this.isFocus) {
          setTimeout(() => {
            this.$root.showIdentityInput = true
          }, 500)
        } else {
          this.$root.showIdentityInput = true
        }
        this.$root.IdentityNums = []
        this.$el.querySelector('#student_name').blur()
      })
    },
    // 替换匹配列表文字
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
      margin-bottom: .7rem;
    }
    >p{
      font-size: .6rem;
      text-align: center;
      margin-top: .4rem;
      color: #aaa;
      &.title{
        font-size: .8rem;
      }
    }
  }
  .input_box{
    padding: 1rem;
    margin-top: 1.5rem;
    >.placeholder{
      color: #666;
      font-size: .6rem;
      padding: .15rem .3rem;
    }
    >.tip{
      border-top: 0;
      border-radius: 0 0 2px 2px;
      background: #FFF;
      -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.2);
      box-shadow: 0 1px 3px rgba(0,0,0,0.2);
      margin: 1px;
      margin-top: 0;
      z-index: 2;
      position: relative;
      >ul{
        font-size: 16px;
        line-height: 1.5rem;
        text-align: left;
        color: #333;
        >li{
          padding-left: .5rem;
          transition: background-color .3s;
          >button{
            width: 100%;
            display: block;
            text-align: left;
            font-size: .7rem;
            line-height: 1.4rem;
            color: #555;
          }
          &:active{
            background-color: #eef3fe;
          }
        }
      }
    }
    >.input_container{
      position: relative;
      >.student_input{
        outline: none;
        background-color: #fff;
        height: 2.2rem;
        width: 100%;
        border-radius: 0.1rem 0 0 0.1rem;
        padding: 0 0.5rem;
        padding-right: 3.8rem;
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
        right: 2px;
        top: 2px;
        height: 2.02rem;
        width: 3.3rem;
        background: #fbfbfb;
        color: #666;
        font-size: .75rem;
        white-space: nowrap;
        letter-spacing: -1px;
        box-sizing: inherit;
        border-left: none;
      }
    }
  }
</style>
