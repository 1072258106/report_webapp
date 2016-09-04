<template>
  <div class="selectdorm page_content">
    <t-nav title="选择宿舍" :show-back="false"></t-nav>
    <crumb :nav-list="navList"></crumb>
    <template  v-for="(unitKey, unit) in dorms">
    <template  v-for="(buildNumKey, buildNum) in unit">
    <template  v-for="(floorKey, floor) in buildNum">
      <div class="selectdorm_body">
        <header>
          {{unitKey}}单元{{buildNumKey}}栋{{floorKey}}层
        </header>
        <section>
          <div class="dorm">
            <ul>
              <li :class="{'disabled': dorm.insert_dorm ==1 && dorm.surplus_beds_num == 0}" @click="goSelBed(dorm.id, dorm.dorm_num, dorm.galleryful, dorm.surplus_beds_num, dorm.selected_beds, dorm.insert_dorm)" v-for="dorm in floor" :style="{'background-color': dormColors[$index].bgcolor}">
                <h4 :style="{ 'color': dormColors[$index].textcolor }">{{dorm.dorm_num}}</h4>
                <span class="surplus">剩余:{{dorm.surplus_beds_num}}/{{dorm.pivot.allow_stay_num}}</span>
              </li>
            </ul>
          </div>
        </section>
      </div>
    </template>
    </template>
    </template>
    <t-footer></t-footer>
  </div>
</template>

<script>
import { XHeader } from 'vux/src/components/'
import TNav from './components/TNav'
import Crumb from './components/Crumb'
import TFooter from './components/TFooter'
import store from './store'
export default {
  components: {
    XHeader, TNav, Crumb, TFooter
  },
  data () {
    return {
      colorIndex: 0,
      dormColors: [
        {bgcolor: '#FBF2A5', textcolor: '#FA8612'},
        {bgcolor: '#CEDEF8', textcolor: '#4F72E6'},
        {bgcolor: '#98FB98', textcolor: '#6CCF6C'},
        {bgcolor: '#C8EAF3', textcolor: '#327296'},
        {bgcolor: '#FEDCDA', textcolor: '#DA787F'},
        {bgcolor: '#FCA4CE', textcolor: '#FCF2FA'},
        {bgcolor: '#BCD3FC', textcolor: '#3A81D5'},
        {bgcolor: '#E0FFFF', textcolor: '#8FDBDB'}
      ],
      dorms: {},
      // 面包屑导航
      navList: [
        {
          title: '选择宿舍',
          href: 'selectdorm'
        }
      ]
    }
  },
  ready () {
    store.getDorms(this).then(res => {
      this.dorms = res
    }, res => {
      if (res.status === 401) {
        this.$root.showMessage(res.data.message)
        this.$route.router.replace({name: 'checkinfo'})
      }
    })
  },
  methods: {
    goSelBed (currDormId, currDorm, galleryful, surplusBedsNum, selBeds, insertDorm) {
      if (insertDorm !== 0) { // 是插宿
        if (surplusBedsNum === 0) { // 剩余0个床位
          return
        }
        this.$root.showConfirm('该宿舍是插宿舍，确定选择？', () => {
          store.selBed(currDormId, 0, this).then(res => {
            // 选择宿舍成功跳转到最终页面
            this.$route.router.go('final')
          })
        }, () => {})
        return
      }
      let selBedInfo = {
        galleryful: galleryful,
        selected_beds: selBeds,
        dorm_num: currDorm,
        id: currDormId
      }
      window.localStorage.selDromBedInfo = JSON.stringify(selBedInfo)
      this.$route.router.go({name: 'selectbed'})
    }
  }
}
</script>

<style lang="less" scoped>
  .selectdorm{
    background-color: #f5f5f5;
    >.selectdorm_body{
      background-color: #fff;
      margin-top: .5rem;
      overflow:hidden;
      padding-bottom: .5rem;
      >header{
        color:#999;
        font-size: .6rem;
        text-align: center;
        padding:.5rem 0;
      }
      >section{
        position: relative;
        overflow: hidden;
        >.dorm{
          >ul{
            >li{
              width: 45%;
              float: left;
              height: 5rem;
              position: relative;
              &.disabled{
                background-color: #ddd!important;
                >h4{
                  color: #BFBFBF!important;
                }
                >span{
                  color: #999!important;
                }
              }
              &:nth-child(even){
                float: right;
              }
              &:not(:last-child){
                margin-bottom: 10px;
              }
              >h4{
                position: absolute;
                top: 50%;
                left: 50%;
                font-size: 1rem;
                color:#555;
                transform: translate(-50%,-50%);
              }
              >.surplus{
                position: absolute;
                left: .3rem;
                bottom: .2rem;
                font-size: .6rem;
                color: #666;
              }
            }
          }
        }
      }
    }
  }
</style>
