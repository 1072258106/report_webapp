<template>
  <div class="page_content">
    <t-nav :show-back="false" title="完成报到"></t-nav>
    <p class="info">已完成自助报到,接下来请到服务站完成其他流程。</p>
    <group>
      <cell title="学号" :value="userInfo.student_num"></cell>
      <cell title="姓名" :value="userInfo.student_name"></cell>
      <cell title="性别" :value="userInfo.gender_title"></cell>
      <cell title="班级" :value="userInfo.student_class ? userInfo.student_class.class_name.short_name : ''"></cell>
      <cell title="联系电话" :value="userInfo.tel"></cell>
      <cell title="身份证" :value="userInfo.id_card"></cell>
      <cell title="报道时间" :value="userInfo.report_time"></cell>
    </group>
    <group :title="'以下信息如果需要修改 请到' + $root.modifyInfoAddr">
      <cell title="你选择的宿舍" :value="dorm"></cell>
      <cell title="身高(cm)" :value="userInfo.student_info ? userInfo.student_info.height : ''"></cell>
      <cell title="体重(kg)" :value="userInfo.student_info ? userInfo.student_info.weight : ''"></cell>
      <div class="remarks weui_cell" v-if="userInfo.student_info != undefined && userInfo.student_info.remarks != ''">
        <header>备注</header>
        <p>{{userInfo.student_info.remarks}}</p>
      </div>
    </group>
    <group v-if="userInfo.dorm_selection !=undefined && userInfo.dorm_selection.dorm.insert_dorm == 0" :title="userInfo.dorm_selection?userInfo.dorm_selection.dorm.dorm_num+ ' 的室友':''">
      <p class="weui_media_desc">
        <span v-if="roomMates.length === 0">你是第一个选择 {{userInfo.dorm_selection ? userInfo.dorm_selection.dorm.dorm_num : ''}} 宿舍的。过一会在来看看吧。</span>
        <span v-for="mate in roomMates">{{mate.student_name}}</span>
      </p>
    </group>
    <div class="exit_btn"><x-button @click="$root.logOut()" type="warn">退出登陆</x-button></div>
    <t-footer></t-footer>
  </div>
</template>
<script>
  import { Group, Cell, XButton } from 'vux/src/components'
  import TNav from './components/TNav'
  import TFooter from './components/TFooter'
  import store from './store'
  export default{
    components: {
      TNav, Group, Cell, TFooter, XButton
    },
    computed: {
      // 选择的宿舍
      dorm () {
        if (this.userInfo.dorm_selection) {
          if (this.userInfo.dorm_selection.dorm.insert_dorm !== 0) {
            return this.userInfo.dorm_selection.dorm.dorm_num
          } else {
            return this.userInfo.dorm_selection.dorm.dorm_num + ' (' + this.userInfo.dorm_selection.bed_num + '号床)'
          }
        }
        return ''
      }
    },
    data () {
      store.getMe(this).then(res => {
        this.userInfo = res.student
        store.getRoomMates(this).then(res => {
          this.roomMates = res
        })
      }, res => {
        if (res.status === 401) {
          this.$root.showMessage(res.data.message)
          this.$route.router.replace({name: 'login'})
        }
      })
      return {
        userInfo: {},
        roomMates: []
      }
    }
  }
</script>

<style scoped lang="less">
  .info{
    margin: .5rem 1rem 0 1rem;
    padding: .7rem;
    color: #3c763d;
    background-color: #dff0d8;
    font-size: .65rem;
    text-align: center;
  }
  .remarks{
    display: block;
    header{
      width: 100%;
    }
    p{
      color: #666;
      font-size: .7rem;
      margin-top: .3rem;
      width: 100%;
      word-break: break-word;
    }
  }
  .weui_media_desc{
    overflow: hidden;
    padding: .2rem;
    >span {
      color: #666;
      font-size: .7rem;
      line-height: 2;
      padding: .2rem;
      float: left;
    }
  }
  .exit_btn{
    padding: 1rem;
  }
</style>
