<template>
  <div class="cart">
    <div class="cart__text" @click="toggleCartVisibility">Корзина ({{productsCount}})</div>
    <div class="cart__modal" v-show="isOpened">
      <div class="cart__modal__overlay" @click="toggleCartVisibility"></div>
      <div class="cart__modal__body">
        <div class="cart__table__wrapper">
          <div class="cart__table">
            <div class="cart__table__row">
              <div class="cart__table__th">Наименование</div>
              <div class="cart__table__th">Кол-во</div>
              <div class="cart__table__th">Цена</div>
              <div class="cart__table__th"></div>
            </div>
            <div class="cart__table__row" v-if="!products.length">
              <div class="cart__table__td">Пусто</div>
              <div class="cart__table__td"></div>
              <div class="cart__table__td"></div>
              <div class="cart__table__td"></div>
            </div>
            <CartProduct v-for="(item,idx) in products"
                         :key="idx"
                         :product="item"
                         @deleteProduct="deleteProduct" />

          </div>
        </div>
        <div class="cart__total">Общая стоимость: <strong>{{total}}  &#x20bd;</strong></div>
      </div>
    </div>
  </div>
</template>

<script>
import CartProduct from "./CartProduct";
export default {
  name: 'Cart',
  components: {CartProduct},
  props: {
    products: Array
  },
  data: ()=>({
    isOpened: false,
  }),
  watch:{
  },
  computed:{
    productsCount(){
      return this.products.length ? this.products.reduce((cur,el)=>(cur + (el.count !== '' ? el.count : 0)),0) : 0
    },
    total(){
      return this.products.length ? this.products.reduce((cur,el)=>(parseFloat((cur + el.priceRub * el.count).toFixed(2))),0) : 0
    }
  },
  methods: {
    toggleCartVisibility(){
      this.isOpened = !this.isOpened
    },
    deleteProduct(product){
      const idx  = this.products.findIndex(el=>el.productId === product.productId)
      this.products.splice(idx,1)
    }
  },
}
</script>


<style lang="scss">
.cart{
  @media (max-width: 550px){
    font-size: 12px;
  }

  &__text{
    text-align: right;
    margin-bottom: 10px;
    cursor: pointer;

    &:hover{
      color: red;
    }
  }
  &__modal{
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    z-index: 2;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding-top: 10vh;

    &__overlay{
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background-color: rgba(0,0,0,.5);
      z-index: 3;
      cursor: pointer;

      &:before{
        content: '\00d7';
        color: #fff;
        display: block;
        height: 40px;
        width: 40px;
        font-size: 40px;
        position: absolute;
        top: 10px;
        right: 10px;
      }
    }

    &__body{
      background-color: #fff;
      width: 100%;
      max-width: 1200px;
      padding: 20px;
      z-index: 4;
      max-height: 80vh;
      display: flex;
      flex-direction: column;
    }
  }

  &__table{
    display: table;
    border-spacing:5px;
    width: 100%;
    border-collapse: collapse;

    &__wrapper{
      height: 100%;
      overflow: auto;
    }

    &__row{
      display: table-row;
      width:auto;

      &:nth-child(even){
        background-color: #ececec;
      }
    }

    &__td,
    &__th{
      display: table-cell;
      padding: 10px;
      vertical-align: middle;
      text-align: left;

      &.text-right{
        text-align: right;
      }

      &:not(:first-child){
        width: 20%;
      }

      &:first-child{
        width: 40%;
      }
    }

    &__th{
      font-weight: bold;
    }
  }

  &__total{
    margin-top: 20px;
    text-align: right;
  }
}
</style>