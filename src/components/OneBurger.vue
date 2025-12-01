<template>
  <div class="burger-item">
    <h4>{{ burger.name }}</h4>
    <img :src="burger.url" style="width: 197px">
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li>
        Contains <span class = "ingredient">lactose</span>:{{ burger.lactose ? ' yes' : ' no' }}
      </li>
      <li>
        Contains <span class = "ingredient">gluten</span>:{{ burger.gluten ? ' yes' : ' no' }}
      </li>
     </ul>
     <div class="burger-amout">
        <span>Amount: </span>
        <button v-on:click="decrease">-</button>
        {{ amountOrdered }}
        <button v-on:click="increase">+</button> 
      </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

data: function () {
  return { 
    amountOrdered: 0
    }
  }, 

methods: {
  increase() {
    this.amountOrdered++;
    this.$emit('orderedBurger', {
      name: this.burger.name, 
      amountOrdered: this.amountOrdered
    });
  },
  decrease() {
    if  (this.amountOrdered > 0){
      this.amountOrdered--; 
      this.$emit('orderedBurger', {
        name: this.burger.name, 
        amountOrdered: this.amountOrdered
        });
      }  
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>