<template>
  <div>
    <div class="w">
      <index-nav :categoryList="categoryList"></index-nav>
      <Banner :bannerList="bannerList"></Banner>
    </div>
    <Floor :floorList="floorList"></Floor>
  </div>
</template>
<script>
import axios from 'axios'

import Floor from './components/floor'
import IndexNav from './components/nav'
import Banner from './components/banner'

export default{
  name: 'Index',
  components: {
    IndexNav,
    Banner,
    Floor
  },
  data () {
    return {
      categoryList: [], // 分类导航
      floorList: [], // 商品楼层
      bannerList: [] // banner图
    }
  },
  methods: {
    getIndexInfo () {
      axios.get('../../../static/mock/index.json')
      .then((res) => {
        const msg = res.data
        this.categoryList = msg.data.categoryNav
        this.floorList = msg.data.floorList
        this.bannerList = msg.data.swiperList
      })
      .catch((arr) => {
      })
    }
  },
  mounted () {
    this.getIndexInfo()
  }
}
</script>
<style lang="stylus" scoped>
  
</style>
