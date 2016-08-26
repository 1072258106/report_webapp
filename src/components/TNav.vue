<template>
<x-header :right-options="{showMore: true}"  @on-click-more="showMenus = true" :left-options="{showBack: showBack, backText: ''}" :style="{'background-color':bgcolor}">
  <span :style="{'color': textcolor}">{{title}}</span>
</x-header>
<actionsheet :menus="menus" @on-click-menu-menu2="showSuccess = true" :show.sync="showMenus" show-cancel></actionsheet>
<toast :show.sync="showSuccess">退出登录成功!</toast>
</template>

<script>
  import { XHeader, Actionsheet, Toast } from 'vux/src/components/'
  export default {
    components: {
      XHeader, Actionsheet, Toast
    },
    props: {
      // 导航栏颜色
      bgcolor: {
        type: String,
        default: '#f5f5f5'
      },
      textcolor: {
        type: String,
        default: '#333'
      },
      title: String,
      showBack: {
        type: Boolean,
        default: true
      }
    },
    data () {
      return {
        menus: {
          menu1: window.localStorage.uname,
          menu2: '<span style="color:red">退出登录</span>'
        },
        showMenus: false,
        showSuccess: false
      }
    }
  }
</script>

<style scoped>

</style>
