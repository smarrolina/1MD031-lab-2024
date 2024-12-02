<template>
  <div>
    <header>
      <h1>Best Burgers In Town</h1>
      <img
        src="https://thatsup.website/storage/275/20463/_DSF0014.jpg?v=1687783191"
        alt="Header Image"
      />
    </header>

    <main>
      <section id="burger-section">
        <h3>Find your perfect burger</h3>
        <p>
          Looking for the ultimate burger experience? <br />
          Our menu offers a variety of mouthwatering options, each crafted with
          the freshest ingredients and unique flavors.
        </p>
        <div class="burger-container">
          <Burger 
          v-for="burger in burgers" :key="burger.name" :burger="burger" @add-to-order="addToOrder($event)" />
        </div>
      </section>

      <section id="order-info">
        <h3>Customer Information</h3>
        <form>
          <p>
            <label for="firstlastname">Full Name</label><br />
            <input
              type="text"
              id="firstlastname"
              v-model="fullName"
              required="required"
              placeholder="First and Last name"
            />
          </p>
          <p>
            <label for="email">E-mail</label><br />
            <input
              type="text"
              id="email"
              v-model="email"
              required="required"
              placeholder="E-mail address"
            />
          </p>
          <p>
            <label for="phone">Phone Number</label><br />
            <input
              type="tel"
              id="phone"
              v-model="phone"
              required="required"
              placeholder="Phone Number"
            />
          </p>
          <p>
            <label for="payment">Payment Options</label><br />
            <select id="payment" v-model="payment">
              <option>Card</option>
              <option>Credit</option>
              <option>Check</option>
              <option>Klarna</option>
              <option>Invoice</option>
            </select>
          </p>
          <p>
            <label>Gender</label><br />
            <input type="radio" id="male" v-model="gender" value="male" />
            <label for="male">Male</label><br />
            <input type="radio" id="female" v-model="gender" value="female" />
            <label for="female">Female</label><br />
            <input type="radio" id="nogender" v-model="gender" value="nogender" />
            <label for="nogender">Do not wish to provide</label>
          </p>
          <div class="map-container">
            <div id="map" v-on:click="setLocation">
            <div
              v-bind:style="{left: location.x + 'px', top: location.y + 'px'}" id="target">
              T
            </div>
            </div>
          </div>
          <button type="button" v-on:click="addOrder">
            <img
              src="https://cdn-icons-png.flaticon.com/512/5278/5278682.png"
              style="width: 22px; height: 22px;"
            />
            Place my order
          </button>
        </form>
      </section>
    </main>
  </div>
</template>


<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

/*console.log(menu); // Kontrollera om JSON-innehållet laddas korrekt*/
const socket = io("localhost:3000");

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurgers: {},
      fullName: "",
      email: "",
      phone: "",
      payment: "Card",
      gender: "",
      location: {x: 0, y: 0}
    };
  },
  methods: {
    setLocation: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top,
      };
      this.location.x = event.clientX - offset.x;
      this.location.y = event.clientY - offset.y;
      console.log("Location set to:", this.location);
    },
    
    addOrder: function () {
      if (Object.keys(this.orderedBurgers).length === 0) {
        alert("Please add at least one burger to your order.");
        return;
      }

      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y },
        orderItems: this.orderedBurgers,
        customerInfo: {
          name: this.fullName,
          email: this.email,
          phone: this.phone,
          gender: this.gender,
          paymentMethod: this.payment
        },
      });
      console.log("Order sent:", {
        orderId: this.getOrderNumber(),
        details: this.location,
        orderItems: this.orderedBurgers,
      });
    },
    
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },
    
    addToOrder: function (burgerName) {
      if (!this.orderedBurgers[burgerName]) {
        this.orderedBurgers[burgerName] = 1;
      } 
      else {
        this.orderedBurgers[burgerName]++;
      }
      console.log(`Added ${burgerName}:`, this.orderedBurgers);
    },
    
    removeFromOrder: function (burgerName) {
      if (this.orderedBurgers[burgerName] && this.orderedBurgers[burgerName] > 0) {
        this.orderedBurgers[burgerName]--;
        if (this.orderedBurgers[burgerName] === 0) {
          delete this.orderedBurgers[burgerName];
        }
      }
      console.log(`Removed ${burgerName}:`, this.orderedBurgers);
    },
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap");

/*ATT EN HEADERBORDER FINNS*/
header {
  position: relative;
  width: 99%;
  height: 200px;
  overflow: hidden;
  margin-bottom: 20px;
  border-radius: 10px;
  border: 5px solid rgb(167, 80, 80);
}

/*ATT BILDEN BLIR RÄTT STORLEK I HEADER*/
header img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0.8;
}

/*ATT TEXTEN I HEADER BLIR RÄTT*/
header h1 {
  position: relative;
  color: rgb(255, 255, 255);
  font-family: "Cormorant", serif;
  font-size: 36pt;
  text-align: center;
  z-index: 1;
  margin: 0;
  padding: 0;
  line-height: 200px;
  text-shadow: 2px 2px 4px rgba(92, 0, 0, 0.7);
}



/* BAKGRUND FÖR SEKTION 1 --> id */
#burger-section {
  color: #ffffff;
  background-color: rgba(145, 0, 0, 0.7);
  text-align: left;
  padding: 20px;
  font-size: 20px;
  font-family: "Didot", serif;
}

/* BESTÄLLNMINGSKNAPPEN! */
button:hover {
  background-color: rgb(34, 125, 54);
  margin-bottom: 20px;
  cursor: pointer;
}

/* BORDERS FÖR VARJE SEKTION */
section {
  margin-bottom: 20px;
  border: 5px solid rgb(167, 80, 80);
  border-radius: 15px;
  padding: 20px;
}

/* HÄR SKA MIN GRID VA! */
.burger-container {
  display: grid; 
  grid-template-columns: repeat(3, 1fr); 
  gap: 10px; 
  justify-content: center; 
  padding: 20px; 
}

.map-container {
  width: 700px; 
  height: 400px; 
  overflow: scroll;
  border: 2px solid rgb(167, 80, 80); 
}

#map {
  width: 1920px;
  height: 1078px; 
  background: url("/img/polacks.jpg") no-repeat center center;
  background-size: cover;
  position: relative;
}

#target {
  position: absolute;
  background: black;
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  text-align: center;
  line-height: 20px;
  font-weight: bold;
}
</style>
