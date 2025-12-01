<template>

<header id="header">
       <h1>Välkommen till BurgerOnline</h1>
        <img src="/img/logga.png" id="header-img">
    </header>
    <nav><h2> Select your burger of choice </h2></nav>
    <main>

        <section id="burgers">
            <h3 class="burgers-text">This is where you execute burger selection</h3>

            <div class="burgers-grid">
                <Burger 
                v-for="(burger, index) in burgers" 
                :key="burger.name" 
                :burger="burger" 
                @orderedBurger="addToOrder($event)"
                />
            </div>
        </section>
      <section id="order-info">
            <h3> Customer information </h3>
            <p>This is where you provide neccessary information</p>
        </section>
        <section id="contact">
            <form>
                <h4>Delivery information:</h4>
                <p>
                    <label for="Full name">Full name</label><br>
                    <input type = "text" id="Full name" v-model= "fullName" required="required" placeholder="First-and Last name">
                </p>
                <p>
                    <label for="E-mail">E-mail</label><br>
                    <input type = "email" v-model="email" required="required" placeholder="E-mail address">
                </p>
                <p>
                    <label for="payment options">Payment options</label><br>
                    <select v-model="paymentOptions" name="rcp">
                        <option selected="selected">Credit card</option>
                        <option>Klarna</option>
                        <option>Pay pal</option>
                        <option>Swish</option>
                        <option>Cash</option>
                    </select>
                </p>
                <p>
                    <label>Please indivate point of delivery:</label>
                </p>    
                <section id="map-container"> 
                  <div class="map-scrollbox"> 
                    <div id="map" v-on:click="setLocation">
                      <div class="target-dot"
                      :style="{ left: location.x + 'px', top: location.y + 'px' }"> 
                      T
                      </div>
                    </div>
                  </div>
                </section> 
                <p>
                    <label>Gender</label><br>

                    <input type ="radio" id="male" value="male" v-model="gender">
                    <label for="male">Male</label><br>

                    <input type ="radio" id="female" value="female" v-model="gender">
                    <label for="female">Female</label><br>

                    <input type ="radio" id="non-binary" value="non-binary" v-model="gender">
                    <label for="non-binary">Non-binary</label><br>

                    <input type ="radio" id="undisclosed" value="undisclosed" v-model="gender">
                    <label for="undisclosed">Undisclosed</label><br>
                </p>

                <button type="button" v-on:click="placeOrder">
                    <img src="/img/Green_check.png" style="width: 20px">
                     Send info
                </button>
            </form>
        </section>
    </main>
    <footer>
    </footer>

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, url, kCal, gluten, lactose){
    this.name = name 
    this.url = url
    this.kCal = kCal
    this.gluten = gluten
    this.lactose = lactose
  }

  const burgers = [
    new MenuItem('The Chicken burger', '/img/burgers-grand-chicken-sandwich.png', 750, false, true),
    new MenuItem('Halloumi Burger', '/img/product_gdl-umami-halloumi12.png', 600, true, true),
    new MenuItem('The Original Burger', '/img/69232_c4_unicornburger_singel_int_hemsida_1920x1787px_se.png', 500, true, false),
  ];

  console.log(burgers);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      fullName: "",
      email: "",
      paymentOptions: "Credit card",
      gender: "",
      orderedBurgers: {},

      location:{
        x: 0,
        y: 0
      }
    }
  },
  methods: {

  addToOrder(event) {
    this.orderedBurgers[event.name] = event.amountOrdered; 
  },

  setLocation(event) {
    const offset = event.currentTarget.getBoundingClientRect();

    this.location.x = event.clientX - offset.left;
    this.location.y = event.clientY - offset.top;

    console.log("Click location:", this.location.x, this.location.y);
  },

  placeOrder(event) {
    console.log("Full name:", this.fullName);
    console.log("Email:", this.email);
    //console.log("Street:", this.streetAdress);
    //console.log("House number:", this.houseNumber);
    console.log("Payment:", this.paymentOptions);
    console.log("Gender:", this.gender);
    console.log(this.orderedBurgers);

    socket.emit("addOrder", { 
        orderId: this.getOrderNumber(),
        details: { 
          x: this.location.x,
          y: this.location.y,
          fullName: this.fullName,
          email: this.email,
          payment: this.paymentOptions,
          gender: this.gender,
        },
    
        orderItems: this.orderedBurgers
      });
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&display=swap');
/* how to comment*/

body {
    font-size: 12pt;
    font-family: Playfair Display, serif;
}
p {
    color: black;
}

h1 {
    font-family: Playfair Display, serif;
    font-size: 36pt;
    color: black;
}



main, header, footer, nav ul {
    max-width: none;
    width: 100%;
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
    background-color: rgb(250, 250, 250);
    border: 2px solid rgb(245, 245, 245);
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 100px;
}

header h1 {
    width:40rem;
    margin: 0 auto;
    text-align: center;
}

#header-img {
    max-width: 10rem;
    display: block;
    margin: 5px auto;

}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}

.ingredient{
   font-weight: bold;
}

#burgers {
    background-color: rgb(250, 250, 250);
    color: black;
    margin: 10px;
    border: 2px solid rgb(245, 245, 245);
    padding: 10px;
    margin-bottom: 10px;
}

.burgers-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2rem;
    justify-items: center;
}

.burger-item {
    text-align: center;  
}

#order-info {
    background-color: rgb(250, 250, 250);
    border: 2px solid rgb(245, 245, 245);
    padding: 15px;
    margin-bottom: 20px;
}

#map-container {
  margin-top: 20px;
}

.map-scrollbox {
  width: 1300px;       
  height: 300px;
  border: 2px solid #ccc;
  overflow: scroll;   
  position: relative; 
}

.target-dot {
  position: absolute;
  width: 20px;
  height: 20px;
  background: black;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
}

#map {
  width: 1920px;              
  height: 1078px;
  background: url("/img/polacks.jpg");
  background-size: 100% 100%;
  background-position: center;
  position: relative;
}

#header {
    position: relative;
    height: 300px;
    overflow: hidden;
    margin:10px;
}

#header-img{
    width: 100%;
    height: auto;
    opacity: 0.5;
    display: block;
    margin: 0 auto;
}

#header h1{
    position: absolute;
        top: 50%;            
        left: 50%;           
        transform: translate(-50%, -50%); 

        color: black;
        padding: 10px;
        font-size: 32pt;
        z-index: 10;
}
         
main {
    background-color: white;
}

button:hover {
    background-color: lightgreen;
    cursor: pointer;
}

button {
  margin-top: 10px;
  padding: 8px 10px;
}

section {
       margin: 20px;
}
</style>