<template>
  <div>
    <Crumbs></Crumbs>
    <div class="cart-wrap w" v-show="isCartProductVoList">
      <cart-header @selectAllChecked="allCheckeds"></cart-header>
      <cart-list :cartProductVoList="cartProductVoList"
        @selectGoods="selectGoods"
        @deleteProduct="deleteProduct"></cart-list>
      <cart-footer 
        @selectAllChecked="allCheckeds"
        @deleteProduct="deleteProduct" 
        :cartProductVoList="cartProductVoList"></cart-footer>
    </div>
    <div class="cart-wrap w" v-show="!isCartProductVoList">
      <p class="err-tip">
        <span>您的购物车空空如也，</span>
        <router-link to="/">立即去购物</router-link>
      </p>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import Crumbs from '../common/crumbs'
import CartHeader from './components/cart-header'
import CartFooter from './components/cart-footer'
import CartList from './components/cart-list'
export default {
  name: 'Index',
  components: {
    Crumbs,
    CartHeader,
    CartFooter,
    CartList
  },
  data() {
    return {
      cartProductVoList: [], // 购物车列表
    }
  },
  computed:{
    // 购物车是否为空
    isCartProductVoList(){
      return  this.cartProductVoList.length > 0 ? true : false
    }
  },
  methods: {
    // 全选与反选
    allCheckeds(opt) {
        // 如果opt为真
        if (opt) {
          // 所有商品为没选中状态
          for(let i = 0; i< this.cartProductVoList.length; i++){
            this.cartProductVoList[i].productChecked = 0
          }
          // 取消全选接口
          axios.get('/api/cart/un_select_all.do', {})
            .then((res) => {
              this.$store.commit('changeTotalPrice', res.data.data.cartTotalPrice)
            })
            .catch((err) => {})
        } else {
          // 所有商品为选中状态
          for(let i = 0; i< this.cartProductVoList.length; i++){
            this.cartProductVoList[i].productChecked = 1
          }
          // 全选接口
          axios.get('/api/cart/select_all.do', {})
            .then((res) => {
              this.$store.commit('changeTotalPrice', res.data.data.cartTotalPrice)
            })
            .catch((err) => {})
        }
        // vuex中changeSelectAll的传值
        this.$store.commit('changeSelectAll', !opt)
    },
    // 选中的购物车数量
    selectGoods (cartArr) {
      this.cartProductVoList = cartArr
    },
    // 购物车列表
    cartList() {
      axios.get('/api/cart/list.do', {})
        .then((res) => {
          // 购物车列表
          this.cartProductVoList = res.data.data.cartProductVoList
          console.log(this.cartProductVoList.length > 0);
          // 总金额
          this.$store.commit('changeTotalPrice', res.data.data.cartTotalPrice)
          if(this.cartProductVoList.length > 0){
            this.$store.commit('changeSelectAll', res.data.data.allChecked)
          }else{
            this.$store.commit('changeSelectAll', false)
          }
          
        })
        .catch((err) => {})
    },
    // 移除某个商品或多个商品
    deleteProduct(params){
      if(params != '' && params != undefined ){
        axios.get('/api/cart/delete_product.do', {
          params:{
            productIds:params
          }
        })
        .then((res) => {
          // 购物车列表
          this.cartProductVoList = res.data.data.cartProductVoList
          // 总金额
          this.$store.commit('changeTotalPrice', res.data.data.cartTotalPrice)
          // 是否全选
          this.$store.commit('changeSelectAll', res.data.data.allChecked)
        })
        .catch((err) => {})
      }
    }
  },
  mounted() {
    this.cartList()
  }
}
</script>
<style lang="stylus">
  .cart-wrap .cart-table .cell-info {
    width: 400px;
    padding: 0 10px;
}
.cart-wrap .cart-table .cell-price {
    width: 100px;
    text-align: center;
}
.cart-wrap .cart-table .cell-count {
    width: 200px;
    text-align: center;
}
.cart-wrap .cart-table .cell-total {
    width: 100px;
    text-align: center;
}
.cart-wrap .cart-table .cell-opera {
    width: 110px;
    text-align: center;
}
.cart-select-all
  float: left
  margin: 12px 10px 0 0 
  a
    display: block
    border: 1px solid #ccc
    width: 15px
    height: 15px
    cursor: pointer
    line-height: 15px
    position: relative
    -webkit-user-select: none;
    -moz-user-select: none; 
    -ms-user-select: none;
    -o-user-select: none;
    user-select: none;
    &.all-select-act
      background: #c60023
      color: #fff
      border: 1px solid #c60023
      text-align: center
</style>