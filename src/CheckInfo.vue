<template>
  <div class="page_content">
    <t-nav title="确认报道"></t-nav>
    <group>
      <cell title="学号" :value="userInfo.student_num"></cell>
      <cell title="姓名" :value="userInfo.student_name"></cell>
      <cell title="性别" :value="userInfo.gender_title"></cell>
      <cell title="联系电话" :value="userInfo.tel"></cell>
      <cell title="身份证" :value="userInfo.id_card"></cell>
      <cell title="毕业中学" :value="userInfo.graduate_school"></cell>
    </group>
    <group title="输入身高体重用于订做校服(校服普遍偏大)">
      <selector :value.sync="height" placeholder="请选择身高" title="身高" :options="heightList"></selector>
      <x-input :value.sync="weight" title="体重" name="userweight" placeholder="请输入体重"></x-input>
    </group>
    <!-- remarks -->
    <group title="备注">
      <x-textarea :value.sync="remarks" :max="20" placeholder="请填写个人特殊情况(可不写)"></x-textarea>
    </group>
    <div class="submit_btn"><x-button :text="submitBtn.title" :disabled="submitBtn.disabled" @click="processButton()" type="primary"></x-button></div>
    <t-footer></t-footer>
    <confirm confirm-text="确认" cancel-text="取消" :show.sync="ensureSub" title="提示" @on-cancel="onCancel()" @on-confirm="onConfirm()">
      <p style="text-align:center;">确定报道？</p>
    </confirm>
    <alert :show.sync="showAlert" title="提示">请填写身高、体重</alert>
  </div>
</template>
<script>
  import { Group, Cell, XButton, Confirm, Selector, XInput, XTextarea, Alert } from 'vux/src/components'
  import TFooter from './components/TFooter'
  import TNav from './components/TNav'
  import store from './store'
  export default{
    components: {
      TFooter, TNav, Group, Cell, XButton, Confirm, Selector, XInput, XTextarea, Alert
    },
    data () {
      return {
        userInfo: {},
        // 提交按钮
        submitBtn: {
          title: '确定报道',
          disabled: false
        },
        ensureSub: false,
        tipText: '',
        showAlert: false,
        // 身高、体重
        height: '',
        weight: '',
        remarks: '',
        heightList: ['200', '195', '190', '185', '180', '175', '170', '165', '160', '155', '150', '145', '140']
      }
    },
    methods: {
      // 点击提交按钮
      processButton () {
        if (this.height.length === 0 || this.weight.length === 0) {
          this.showAlert = true
        } else {
          this.submitBtn.title = '提交中'
          this.submitBtn.disabled = true
          this.ensureSub = true
        }
      },
      onCancel () {
        this.submitBtn.title = '确定报道'
        this.submitBtn.disabled = false
      },
      onConfirm () {
        store.report(this.height, this.weight, this.remarks, this).then(res => {
          this.$route.router.go({name: 'reportok'})
        })
      }
    },
    ready () {
      // 获取登陆者信息
      store.getMe(this).then(res => {
        this.userInfo = res.student
        if (this.userInfo.is_report === 1 && this.userInfo.whether_has_dorm === 1) {
          // 直接跳转到最终页面
          this.$route.router.go('final')
        } else if (this.userInfo.is_report === 1) {
          // 跳转到登记成功页面
          this.$route.router.go('reportok')
        }
      }, res => {
        if (res.status === 401) {
          this.$route.router.replace({name: 'login'})
        }
      })
    }
  }
</script>

<style scoped lang="less">
  .submit_btn{
    padding: 2rem 1rem;
  }
  .weui_input{
    padding-left: 55px;
  }
</style>
