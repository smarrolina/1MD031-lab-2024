<template>
  <div class="burger-box">
    <h4>{{burger.name}}</h4>
    <img v-bind:src="burger.imageUrl">
    <ul>
      <p>{{ burger.kCal }} kCal</p>
      <div class="allergies">
        <p v-if="burger.containsGluten">Contains Gluten</p> 
        <p v-if="burger.containsLactose">Contains Lactose</p> 
      </div>
    </ul>
    <div class="amount-control"> Order Amount: 
      <button v-on:click="decreaseOrder" :disabled="amountOrdered <= 0">−</button>
      <span>{{ amountOrdered }}</span>
      <button v-on:click="increaseOrder">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "OneBurger",
  props: {
    burger: {
      type: Object,
      required: true,
    },
  },
  data(){
    return {
      amountOrdered: 0,
    };
  },
  methods: {
    increaseOrder(){
      this.amountOrdered++;
      this.$emit('add-to-order', this.burger.name);
    },
    decreaseOrder(){
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('remove-from-order', this.burger.name); 

      }
    },
  },
};
</script>

<style scoped>

.burger-box {
  display: flex;
  flex-direction: column;
  background-color: rgb(233, 99, 99); 
  padding: 15px; 
  box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.2);
  text-align: center;
  align-items: center;
}

h4 {
  font-size: 25px; 
  font-weight: bold;
  color: #fff; 
  margin: 10px 0; 
}

img {
  width: 200px; 
  height: 200px; 
  object-fit: cover
}

.amount-control {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  margin-top: 10px;
}

.amount-control button {
  width: 30px;
  height: 30px;
  font-size: 16px;
  font-weight: bold;
  color: #000000;
  background-color: #ffffff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.amount-control button:hover {
  background-color: #7f7f7f;
}

.amount-control span {
  font-size: 16px;
  font-weight: bold;
  color: #fff;
}
</style>

