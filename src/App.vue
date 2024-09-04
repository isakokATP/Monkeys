<script setup>
import { ref, computed, nextTick } from "vue";

const colors = ref(["#957DAD", "#D291BC", "#FFDFD3"]);
const change = ref("");

function randomColor() {
  change.value = colors.value[Math.floor(Math.random() * colors.value.length)];
}
setInterval(randomColor, 1000);

const menu = ref(false);
const home = ref(true);
const custom = ref(false);
const cartPopup = ref(false);
//drink option pages and functions
const drinkOptions = ref(false);

function openCartPopup() {
  cartPopup.value = !cartPopup.value;
}

function openPage(page) {
  if (page === "home") {
    home.value = true;
    menu.value = false;
    custom.value = false;
    drinkOptions.value = false;
  } else if (page === "menu") {
    home.value = false;
    menu.value = true;
    custom.value = false;
    drinkOptions.value = false;
  } else if (page === "custom") {
    home.value = false;
    menu.value = false;
    custom.value = true;
    drinkOptions.value = false;
  } else if (page === "drinkOption") {
    home.value = false;
    menu.value = false;
    custom.value = false;
    drinkOptions.value = true;
  }
}

const cartItems = ref([])
let tempId = 0 // เริ่มต้นจาก 0

function generateUniqueId() {
  return ++tempId // เพิ่มค่า tempId และส่งคืนเป็น id
}

let selectedMenuItem = ref({
  id: null,
  name: null,
  description: null,
  image: null,
  drinkType: null,
  sweetness: null,
})

function drinkOptionsItem(item) {
  selectedMenuItem.value = item // Store the selected item from the menu to selectedMenuItem
}


function priceEdit(){
  if(selectedMenuItem.value.drinkType = "cold"){
    return selectedMenuItem.value.price + 10
  } else {
    return selectedMenuItem.value.price
  }
} // bugged , all drinktype returns +10

function addDrinkToCart() {
  if (selectedMenuItem.value.drinkType === undefined || selectedMenuItem.value.sweetness === undefined) {
    alert("please select option")
  } else {
    const newItem = { ...selectedMenuItem.value, id: generateUniqueId(), price: priceEdit() } //priceEdit() bugged
    console.log("🚀 ~ addDrinkToCart ~ newItem:", newItem)

    cartItems.value.push(newItem)

    // Clear selectedMenuItem
    selectedMenuItem.value = {
      id: null,
      name: null,
      description: null,
      image: null,
      drinkType: null,
      sweetness: null,
    }

    openPage("menu")
    openCartPopup()

    console.log(cartItems.value)
  }
}



//List All menu(not custom menu)
const coffeeMenu = ref([
  {
    name: "Capuccino",
    image: "../picture/cappuccino coffee.png",
    price: 40,
  },
  {
    name: "Espresso",
    image: "../picture/espresso coffee.png",
    price: 50,
  },
  {
    name: "Latte",
    image: "../picture/latte coffee.png",
    price: 50,
  },
  // Add more items as needed
])

const TeaMenu = ref([
  {
    name: "Green Tea",
    image: "../picture/green tea.png",
    price: 50
  },
  {
    name: "Matcha Latte",
    image: "../picture/matcha latte.png",
    price: 55
  },
  {
    name: "Black Tea",
    image: "../picture/black tea.png",
    price: 40
  },
  {
    name: "Hojicha Tea",
    image: "../picture/hojicha tea.png",
    price: 50
  },
  {
    name: "Milk tea",
    image: "../picture/milk tea.png",
    price: 50
  },
  // Add more items as needed
])

const MilkMenu = ref([
  {
    name: "Fresh Milk",
    image: "../picture/fresh milk.png",
    price: 50
  },
])

// function removeOrder(orderName) {
//   const filteredCart = cartItems.value.filter(
//     (item) => item.name !== orderName
//   );
//   cartItems.value = filteredCart
// }

function removeOrder(orderId) {
  cartItems.value = cartItems.value.filter(item => item.id !== orderId)
}


const customDrink = ref({
  base: "",
  flavors: [],
  toppings: "", // Change from array to single value
  sweetness: "",
})

const flavors = ref(["Vanilla", "Chocolate", "Caramel", "Hazelnut", "Mint"]); // Add more flavors if needed
const toppings = ref([
  "Whipped Cream",
  "Chocolate Chips",
  "Cinnamon",
  "Caramel Drizzle",
])

// Mapping bases to images
const imageMap = {
  Coffee: "../picture/espresso coffee.png", // Replace with actual paths
  Tea: "../picture/tea_base.png",
  Milk: "../picture/milk_base.png",
}

const toppingCombinationImageMap = {
  "Coffee-Whipped Cream": "../picture/Coffee_with_Whipped_Cream.png",
  "Tea-Chocolate Chips": "../picture/TeaAddChocolate Chips.png",
  "Coffee-Chocolate Chips": "../picture/CoffeewithChocolateChips.png",
  // Add more combinations as needed
}

// Computed property for the custom drink image
const customDrinkImage = computed(() => {
  let baseImage =
    imageMap[customDrink.value.base] || "../picture/questionmark.png"; // Default image if none selected

  // Combine base image with selected topping if any
  const toppingKey = `${customDrink.value.base}-${customDrink.value.toppings}`;
  const toppingImage = toppingCombinationImageMap[toppingKey];

  return toppingImage || baseImage; // Use the topping image if available, otherwise use the base image
});

function addCustomDrinkToCart() {
  if (
    customDrink.value.base &&
    customDrink.value.flavors.length > 0 &&
    customDrink.value.sweetness
  ) {
    cartItems.value.push({
      name: `Custom ${customDrink.value.base}`,
      description: `Base: ${
        customDrink.value.base
      }, Flavors: ${customDrink.value.flavors.join(", ")}, Topping: ${
        customDrink.value.toppings
      }, Sweetness: ${customDrink.value.sweetness}`,
      image: customDrinkImage.value,
      base: customDrink.value.base,
      flavors: customDrink.value.flavors,
      toppings: customDrink.value.toppings,
      sweetness: customDrink.value.sweetness,
      id: generateUniqueId() // Ensure unique ID for each custom drink
    })

    // Clear custom drink form
    customDrink.value = {
      base: "",
      flavors: [],
      toppings: "",
      sweetness: "",
    };

    // Only change page to 'menu' if necessary
    openPage("menu")
    openCartPopup()
  } else {
    alert("Please fill out all fields.");
  }
}

</script>

<template>
  <div class="flex flex-col h-screen">
    <div class="navbar bg-orange-400">
      <div class="drawer w-20">
        <input id="my-drawer" type="checkbox" class="drawer-toggle" />
        <div class="drawer-content">
          <label for="my-drawer" class="btn drawer-button btn-outline bg-white">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              class="inline-block h-5 w-5 stroke-current"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 6h16M4 12h16M4 18h16"
              ></path>
            </svg>
          </label>
        </div>
        <div class="drawer-side z-30">
          <label
            for="my-drawer"
            aria-label="close sidebar"
            class="drawer-overlay"
          ></label>
          <ul class="menu bg-base-200 text-base-content min-h-full w-80 p-4">
            <label
              for="my-drawer"
              aria-label="close-sidebar"
              class="cursor-pointer"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="size-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </label>
            <li><a @click="openPage('menu')">Menu</a></li>
            <li><a @click="openPage('custom')">Custom Your Menu</a></li>
          </ul>
        </div>
      </div>

      <div class="flex-1">
        <a
          class="btn btn-ghost text-xl text-white justify-start"
          @click="openPage('home')"
          >Monkeys-Fresh</a
        >
      </div>

      <!-- cart button -->
      <div class="flex-none">
        <button
          class="btn btn-square btn-ghost bg-white rounded-xl"
          @click="openCartPopup()"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="size-6 justify-end"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M2.25 3h1.386c.51 0 .955.343 1.087.835l.383 1.437M7.5 14.25a3 3 0 0 0-3 3h15.75m-12.75-3h11.218c1.121-2.3 2.1-4.684 2.924-7.138a60.114 60.114 0 0 0-16.536-1.84M7.5 14.25L5.106 5.272M6 20.25a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0Zm12.75 0a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0Z"
            />
          </svg>
        </button>
      </div>
    </div>

    <!-- home page -->
    <div v-show="home" class="flex-grow bg-orange-200">
      <!-- <img class="fixed blur-sm -z-10" src="../picture/8d2a6a3f788f6269.jpg" /> -->
      <div class="w-full flex justify-center">
        <div class="flex flex-col gap-10 mt-20 w-[25%]">
          <div class="button" @click="openPage('menu')">
            <a>Menu</a>
          </div>
          <div class="button" @click="openPage('custom')">
            <a>Custom Your menu</a>
          </div>
        </div>
      </div>

      <div class="flex m-10 justify-center">
        <div class="text-red-500 flex flex-col justify-center items-center cursor-pointer">
          <p :style="{ color: change }" class="text-3xl">RECOMMEND!</p>
          <img
            class="w-60 rounded-2xl shadow-xl"
            src="../picture/espresso coffee.png"
            @click="
              drinkOptionsItem(coffeeMenu[1]);
              openPage('drinkOption');
            "
          />
        </div>
        <div class="text-red-500 flex flex-col justify-center items-center cursor-pointer">
          <p :style="{ color: change }" class="text-3xl">RECOMMEND!</p>
          <img
            class="w-60 ml-5 rounded-2xl shadow-2xl"
            src="../picture/green tea.png"
            @click="
              drinkOptionsItem(TeaMenu[0]);
              openPage('drinkOption');
            "
          />
        </div>
      </div>
    </div>

    <!-- menu page -->
    <div name="menu" v-show="menu" class="flex-grow bg-orange-200">
      <div class="menu-section">
        <h2 class="section-title">COFFEE</h2>
        <div class="menu-slider">
          <div v-for="item in coffeeMenu" :key="item.name" class="menu-item text-black">
            <div
              @click="
                drinkOptionsItem(item);
                openPage('drinkOption');
              "
            >
              <img :src="item.image" :alt="item.name" class="menu-item-image cursor-pointer" />
              <div class="menu-item-text">
                <p>{{ item.name }}</p>
                <p>{{ item.description }}</p>
                <p>{{ item.price }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="menu-section">
        <h2 class="section-title">TEA</h2>
        <div class="menu-slider">
          <div v-for="item in TeaMenu" :key="item.name" class="menu-item text-black">
            <div
              @click="
                drinkOptionsItem(item);
                openPage('drinkOption');
              "
            >
              <img :src="item.image" :alt="item.name" class="menu-item-image cursor-pointer" />
              <div class="menu-item-text">
                <p>{{ item.name }}</p>
                <p>{{ item.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="menu-section">
        <h2 class="section-title">MILK</h2>
        <div class="menu-slider">
          <div v-for="item in MilkMenu" :key="item.name" class="menu-item text-black">
            <div
              @click="
                drinkOptionsItem(item);
                openPage('drinkOption');
              "
            >
              <img :src="item.image" :alt="item.name" class="menu-item-image cursor-pointer" />
              <div class="menu-item-text">
                <p>{{ item.name }}</p>
                <p>{{ item.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="flex justify-center">
        <div class="button" @click="openPage('home')">
          <a>Back</a>
        </div>
      </div>
    </div>
    <div name="custom" v-show="custom"></div>

    <!-- Drink Options page -->
    <div name="drinkOptions" v-show="drinkOptions" class="bg-orange-200">
      <div class="flex justify-center items-center" name="header">
        <img
          class="w-4/12 rounded-2xl shadow-2xl mt-3"
          :src="selectedMenuItem.image"
          :alt="selectedMenuItem.name"
        />
      </div>

      <div class="text-center text-black mt-5">
        <h1 class="text-2xl">Drink type:</h1>
        <input type="radio" v-model="selectedMenuItem.drinkType" value="hot" />
        Hot
        <input type="radio" v-model="selectedMenuItem.drinkType" value="cold" />
        Cold
        <div class="mt-3">
          <h1 class="text-2xl">Sweetness:</h1>
          <input type="radio" v-model="selectedMenuItem.sweetness" value="0%" />
          0%
          <input
            type="radio"
            v-model="selectedMenuItem.sweetness"
            value="25%"
          />
          25%
          <input
            type="radio"
            v-model="selectedMenuItem.sweetness"
            value="50%"
          />
          50%
          <input
            type="radio"
            v-model="selectedMenuItem.sweetness"
            value="75%"
          />
          75%
          <input
            type="radio"
            v-model="selectedMenuItem.sweetness"
            value="100%"
          />
          100%
          
        </div>
        <div class="w-full flex justify-center">
          <div class="flex gap-1 m-2 w-[8%]">
        <button
          @click="
            addDrinkToCart();
          "
          class="btn hover:bg-green-600 text-white text-sm p-1"
        >
          Add to cart
        </button>
        <button
          @click="openPage('menu')"
          class="btn hover:bg-red-600 text-white text-sm p-1"
        >
          Cancel
        </button>
        </div>
        </div>
      </div>
    </div>

    <!-- Custom Page -->
    <div v-show="custom" class="bg-orange-200 flex-grow p-4">
      <div class="flex flex-col items-center">
        <h1 class="text-3xl mb-4 text-black">Create Your Custom Drink</h1>

        <!-- Custom Drink Form -->
        <div class="w-full max-w-md bg-white p-4 rounded shadow-md">
          <h2 class="text-xl mb-2">Select Ingredients</h2>

          <!-- Display Image Based on Selected Base -->
          <!-- Custom Drink Form -->
          <div class="mb-4">
            <img
              :src="customDrinkImage"
              alt="Custom Drink"
              class="w-32 h-32 object-cover rounded"
            />
          </div>

          <div>
            <label class="block mb-2">Select Base:</label>
            <select v-model="customDrink.base" class="border p-2 rounded">
              <option value="">Select a base</option>
              <option value="Coffee">Espresso</option>
              <option value="Tea">Tea</option>
              <option value="Milk">Milk</option>
            </select>
          </div>

          <div class="mt-4">
            <label class="block mb-2">Select Flavors:</label>
            <div v-for="flavor in flavors" :key="flavor">
              <input
                type="checkbox"
                :value="flavor"
                v-model="customDrink.flavors"
              />
              <label>{{ flavor }}</label>
            </div>
          </div>

          <!-- Toppings Selection -->
          <div class="mt-4">
            <label class="block mb-2">Select Topping:</label>
            <select v-model="customDrink.toppings" class="border p-2 rounded">
              <option value="">Select Topping</option>
              <option
                v-for="topping in toppings"
                :key="topping"
                :value="topping"
              >
                {{ topping }}
              </option>
            </select>
          </div>

          <div class="mt-4">
            <label class="block mb-2">Select Sweetness:</label>
            <select v-model="customDrink.sweetness" class="border p-2 rounded">
              <option value="">Select sweetness</option>
              <option value="0%">0%</option>
              <option value="25%">25%</option>
              <option value="50%">50%</option>
              <option value="75%">75%</option>
              <option value="100%">100%</option>
            </select>
          </div>

          <div class="mt-4 flex justify-between">
            <button @click="openPage('home')" class="btn btn-secondary">
              Back
            </button>
            <button @click="addCustomDrinkToCart" class="btn btn-primary">
              Add to Cart
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- cart pop-up -->
    <div
      v-show="cartPopup"
      class="fixed inset-0 z-20 flex items-center justify-center"
    >
      <div
        class="bg-black bg-opacity-50 absolute inset-0"
        @click="openCartPopup()"
      ></div>
      <div
        class="modal-box relative z-30 p-5 bg-white rounded shadow-lg w-[35%] h-[50%]"
      >
        <div class="flex flex-col space-y-2">
          <p>Number Order :{{ cartItems.length }}</p>
          <div v-for="(order, index) in cartItems" :key="index">
            <div class="bg-gray-300 flex justify-between items-center">
              <div class="flex items-center">
                <img
                  :src="order.image"
                  :alt="order.name"
                  class="menu-item-image"
                />
                <div class="m-2">
                  <p>{{ order.name }}</p>
                  <p>{{ order.drinkType }}, {{ order.sweetness }}</p>
                </div>
              </div>
              <div class="text-red-500 pr-4 cursor-pointer">
                <p v-on:click="removeOrder(order.id) ">X</p>
              </div>
            </div>
          </div>
        </div>
        <!-- close button -->
        <div class="mt-3">
        <div class="btn" @click="openCartPopup()">
          <a>Close</a>
        </div>
      </div>
      </div>
    </div>

    <footer
      class="footer footer-center text-base-content p-4 bg-orange-400 z-10"
    >
      <aside>
        <p class="text-white">Monkeys-Fresh Group ©</p>
      </aside>
    </footer>
  </div>
</template>

<style scoped>
.menu-section {
  margin-top: 20px;
  padding: 20px;
}

.section-title {
  font-size: 24px;
  font-weight: bold;
  text-align: left;
  color: rgb(131, 100, 65);
}

.menu-slider {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  gap: 50px;
  padding: 10px;
  scroll-behavior: smooth;
}

.menu-item {
  scroll-snap-align: start;
  min-width: 120px;
  text-align: center;
}

.menu-item-image {
  width: 100px;
  height: 100px;
  object-fit: cover;
  border-radius: 10px;
  box-shadow: 4px 4px 5px
}

.menu-item-text {
  font-size: 16px;
  font-weight: 500;
}

body {
  text-align: center;
  background-color: #ffcc8e;
}

.button {
  position: relative;
  display: inline-block;
  margin: 20px;
}

.MiniButton {
  margin: 10px;
}

.button a {
  color: white;
  font-family: Helvetica, sans-serif;
  font-weight: bold;
  font-size: 20px;
  text-align: center;
  text-decoration: none;
  background-color: #ffa12b;
  display: block;
  position: relative;
  padding: 20px 40px;

  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  text-shadow: 0px 1px 0px #000;
  filter: dropshadow(color=#000, offx=0px, offy=1px);

  -webkit-box-shadow: inset 0 1px 0 #ffe5c4, 0 10px 0 #915100;
  -moz-box-shadow: inset 0 1px 0 #ffe5c4, 0 10px 0 #915100;
  box-shadow: inset 0 1px 0 #ffe5c4, 0 10px 0 #915100;

  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}

.button a:active {
  top: 10px;
  background-color: #f78900;

  -webkit-box-shadow: inset 0 1px 0 #ffe5c4, inset 0 -3px 0 #915100;
  -moz-box-shadow: inset 0 1px 0 #ffe5c4, inset 0 -3pxpx 0 #915100;
  box-shadow: inset 0 1px 0 #ffe5c4, inset 0 -3px 0 #915100;
}

.button:after {
  content: "";
  height: 100%;
  width: 100%;
  padding: 4px;
  position: absolute;
  bottom: -15px;
  left: -4px;
  z-index: -1;
  background-color: #2b1800;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}
</style>