<template>
  <div class="page_content">
    <t-nav title="确认报到"></t-nav>
    <group>
      <cell title="学号" :value="userInfo.student_num"></cell>
      <cell title="姓名" :value="userInfo.student_name"></cell>
      <cell title="性别" :value="userInfo.gender_title"></cell>
      <cell title="班级" :value="userInfo.student_class ? userInfo.student_class.class_name.short_name : ''"></cell>
      <cell title="联系电话" :value="userInfo.tel"></cell>
      <cell title="身份证" :value="userInfo.id_card"></cell>
      <cell title="毕业中学" :value="userInfo.graduate_school"></cell>
    </group>
    <group title="输入身高体重用于订做校服(校服可能偏大)">
      <selector :value.sync="height" placeholder="请选择身高" title="身高(cm)" :options="heightList"></selector>
      <x-input :value.sync="weight" type="number" title="体重(kg)" name="userweight" placeholder="请输入体重"></x-input>
    </group>
    <!-- remarks -->
    <group title="备注">
      <x-textarea :value.sync="remarks" :max="200" placeholder="请填写个人特殊情况(可不写)"></x-textarea>
    </group>
    <div class="submit_btn"><x-button :text="submitBtn.title" :disabled="submitBtn.disabled" @click="processButton()" type="primary"></x-button></div>
    <t-footer></t-footer>
  </div>
</template>
<script>
  import { Group, Cell, XButton, Selector, XInput, XTextarea } from 'vux/src/components'
  import TFooter from './components/TFooter'
  import TNav from './components/TNav'
  import store from './store'
  export default{
    components: {
      TFooter, TNav, Group, Cell, XButton, Selector, XInput, XTextarea
    },
    data () {
      return {
        userInfo: {},
        // 提交按钮
        submitBtn: {
          title: '确定报道',
          disabled: false
        },
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
          this.$root.showAlert('(╥╯^╰╥)身高、体重没有填写完整')
        } else {
          this.submitBtn.title = '提交中'
          this.submitBtn.disabled = true
          this.$root.showConfirm('你输入的体重身高分别是' + this.weight + 'kg,' + this.height + 'cm 可在' + this.$root.modifyInfoAddr + '修改 确认报道?', () => {
            store.report(this.height, this.weight, this.remarks, this).then(res => {
              this.$route.router.go({name: 'reportok'})
            }, res => {
              this.submitBtn.title = '确定报道'
              this.submitBtn.disabled = false
            })
          }, () => {
            this.submitBtn.title = '确定报道'
            this.submitBtn.disabled = false
          })
        }
      }
    },
    ready () {
      // 设置样式
      let weightInput = this.$root.$el.querySelector('.weui_input')
      if (weightInput !== null) {
        weightInput.style.paddingLeft = '3px'
      }
      // 获取登陆者信息
      store.getMe(this).then(res => {
        this.userInfo = res.student
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
