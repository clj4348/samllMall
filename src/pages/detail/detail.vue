<template>
  <div>
    <div class="page-container w">
      商品详情
      <div class="intro-wrap clearfix">
        <goods-banner :detailInfo="detailInfo"></goods-banner>
        <Sku :detailInfo="detailInfo"></Sku>
      </div>
      <goods-info :detailDesc="detailDesc"></goods-info>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import GoodsBanner from './components/goodsBanner'
import GoodsInfo from './components/goodsInfo'
import Sku from './components/sku'

export default{
  name: 'List',
  components: {
    GoodsBanner,
    GoodsInfo,
    Sku
  },
  data () {
    return {
      productId : this.utils.getUrlParam('productId') || '',
      detailInfo: {},
      detailDesc: '',
    }
  },
  methods: {
    getDetail() {
      axios.get('/api/product/detail.do', {
        params:{
          productId: this.productId
        }
      })
      .then((res) => {
        this.detailInfo = res.data.data
        this.detailDesc = res.data.data.detail
      })
      .catch((err) => {

      })
    }
  },
  mounted () {
    this.getDetail()
  }
}
</script>

<style lang="stylus" scoped>
  .intro-wrap{
    overflow: hidden;
  }
</style>
