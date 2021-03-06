<template>
  <div class="main">
    <Cart :products="mergedProducts" />
    <div class="tab__wrapper">
      <div class="tab" v-for="(item,idx) in productsByCategories" :key="idx">
        <h3 :class="['tab__title',{'collapsed': hiddenTabs.indexOf(idx) !== -1}]" @click="toggleTabVisibility(idx)">{{item.categoryName}}</h3>
        <div v-if="hiddenTabs.indexOf(idx) === -1" class="tab__content">
          <div class="tab__product" v-for="(item,idx) in item.products" :key="idx" @click="addToCart(item)">
            <span class="tab__product__name">{{item.name}} <strong>({{item.stock}})</strong></span>
            <span class="tab__product__price">{{item.priceRub}} &#x20bd;</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import data from '../assets/data.json'
import names from '../assets/names.json'

import Cart from "./Cart";

export default {
  name: 'Main',
  components: {
    Cart
  },
  data: ()=>({
    USD_RATE: 74.21,
    hiddenTabs: [],
    mergedProducts: []

  }),
  computed:{
    productsByCategories(){
      const goods = data.Value.Goods
      let temp = {}

      for(const product of goods){
        const categoryId = product['G']
        const productId = product['T']
        const priceUsd = product['C']
        const priceRub = this.usdTorub(priceUsd)
        const stock = product['P']

        const name = names[categoryId]['B'][productId]['N']
        if(!temp[categoryId]){
          temp[categoryId] = {
            categoryName: names[categoryId]['G'],
            products: []
          }
        }
        temp[categoryId].products.push({categoryId,productId,name,priceUsd,priceRub,stock})
      }

      return temp
    }
  },
  methods: {
    usdTorub(usd){
      return parseFloat((usd * this.USD_RATE).toFixed(2))
    },
    toggleTabVisibility(id){
      const idx = this.hiddenTabs.indexOf(id)
      if(idx === -1) {
        this.hiddenTabs.push(id)
      }else{
        this.hiddenTabs.splice(idx,1)
      }
    },
    addToCart(product){
        const idx = this.mergedProducts.findIndex(el=>el.productId === product.productId)

        if(idx === -1){
          const prodTemp = {...product}
          prodTemp.count = 1
          this.mergedProducts.push(prodTemp)
        }else{
          this.mergedProducts[idx].count += 1
        }
    }
  },
}
</script>


<style lang="scss">
.main{
  max-width: 1200px;
  margin: 0 auto;
  padding: 50px 15px;

  @media (max-width: 550px){
    padding: 30px 10px;
  }
}
.tab{
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 0;
  break-inside: avoid;
  text-align: left;
  overflow: hidden;

  &__wrapper{
    column-count: 2;
    grid-gap: 10px;

    @media (max-width: 768px){
      column-count: 1;
    }
  }

  &__title{
    font-weight: bold;
    border-bottom: 1px solid #ccc;
    margin: 0;
    padding: 10px 10px 10px 30px;
    background: #ccc;
    cursor: pointer;
    position: relative;

    &:before{
      content: '';
      display: block;
      width: 0;
      height: 0;
      border-left: 6px solid transparent;
      border-right: 6px solid transparent;
      border-top: 6px solid #2c3e50;
      position: absolute;
      top: 14px;
      left: 10px;
    }

    &.collapsed{
      &:before{
        border-top: none;
        border-bottom: 6px solid #2c3e50;
      }
    }
  }

  &__content{
    display: grid;
    grid-gap: 1px;
    grid-template-columns: 50% 50%;
    background-color: #ececec;

    @media (max-width: 550px){
      grid-template-columns: 100%;
    }
  }

  &__product{
    display: flex;
    justify-content: flex-start;
    align-items: center;
    background-color: #fff;
    padding: 10px;
    cursor: crosshair;

    &:hover{
      background-color: #ececec;
    }
    &:active{
      background-color: #8aff8a;
    }

    &__price{
      white-space: nowrap;
      font-weight: bold;
      font-size: 14px;
      margin-left: auto;
    }

    &__name{
      font-size: 13px;

      strong{
        font-weight: bold;
      }
    }
  }
}
</style>