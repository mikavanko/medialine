<template>
  <div class="cart__table__row">
    <div class="cart__table__td">{{product.name}}</div>
    <div class="cart__table__td">
      <input type="number"
             class="cart__table__count"
             v-model.number="product.count"
             min="0"
      >
      <p class="cart__table__error" v-if="isCountMax">Количество ограничено</p>
    </div>
    <div class="cart__table__td cart__table__price"><strong>{{product.priceRub}} &#x20bd;</strong>/ шт.</div>
    <div class="cart__table__td cart__table__remove text-right" @click="deleteFromCart">Удалить</div>
  </div>
</template>

<script>
export default {
  name: 'CartProduct',
  props: {
    product: Object
  },
  data: ()=>({
    isCountMax: false,
  }),
  watch:{
    'product.count':{
      handler(val){
        if(val > this.product.stock){
          this.product.count = this.product.stock
        }else if(val === this.product.stock){
          console.log('this.isCountMax = true')
          this.isCountMax = true
        }else if(this.isCountMax){
          console.log('this.isCountMax = false')
          this.isCountMax = false
        }
      }
    }
  },
  methods: {
    deleteFromCart(){
      this.$emit('deleteProduct', this.product)
    }
  },
}
</script>


<style lang="scss">
.cart{
  &__table{
    &__remove{
      &:hover{
        color: red;
        cursor: pointer;
      }
    }
    &__price{
      white-space: nowrap;
    }
    &__count{
      width: 50px;
      height: 40px;
      outline: none;
      border: 1px solid #ccc;
      padding: 0 5px;
      display: inline-block;
      vertical-align: middle;
      margin-right: 10px;
    }
    &__error{
      font-size: 13px;
      width: 90px;
      height: 40px;
      display: inline-block;
      vertical-align: middle;
      border: 1px solid red;
      background-color: #ffd5a0;
      color: red;
      padding: 5px;
    }
  }
}
</style>