<template>
<x-header :right-options="{showMore: true}"  @on-click-more="$root.option.showMenus = true" :left-options="{showBack: showBack, backText: ''}" :style="{'background-color':bgcolor}">
  <span :style="{'color': textcolor}">{{title}}</span>
</x-header>
</template>

<script>
  import { XHeader, Toast } from 'vux/src/components/'
  export default {
    components: {
      XHeader, Toast
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
      }
    }
  }
</script>

<style scoped>

</style>
