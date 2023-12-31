<template>
  <nav class="neumorphic-navbar" style="border-radius:5px;height:450px; width: 450px; display: flex; flex-direction: column; justify-content: flex-start; align-items: flex-start; margin-top:60px;" :class="{ 'navbar-hidden': isNavbarHidden }">
    <p style="margin-left: 20px; margin-bottom: 10px; font-size: 12px; font-weight:600;">PRODUCT NAME:  <span style="font-size: 20px; font-weight:500; margin-left:5px; color:rgb(223, 146, 5);">{{ productData.prod_name }}</span></p>

    <div style="display: flex; flex-direction: column; align-items: center; margin-bottom: 20px;">
        <!-- <p>Image:</p> -->
        <img :src="productData.image" alt="Product Image" class="img-fluid" style="max-width: 250px; max-height: 250px; margin-top:20px; margin-left: 90px;">
    </div>

    <p style="margin-left: 20px; margin-bottom: 10px; font-size: 12px; font-weight:700;">UNIT PRICE: <span style="margin-left: 10px; color:rgb(240, 141, 29); font-size:30px;">₱  {{ productData.unit_price }}</span></p>
    <p style="margin-left: 20px; margin-bottom: 10px; font-size: 12px; font-weight:600;" >SIZE: <span style="margin-left: 10px; color:rgb(240, 141, 29); font-size:15px;">{{ getSizeName(productData.size_id) }}</span></p>
    <router-link to="/userproducts" style=" border-radius:5px;width:150px; margin-left:250px; position:absolute; margin-top:345px; font-size:11px; font-weight:500; color:rgb(45, 121, 55)" class="neumorphic-button">  CHOOSE ANOTHER PRODUCT</router-link>
</nav>

  
  <div class="container mt-4" style="margin-left:200px; ">
    <h2 style="margin-left:450px; font-size:25px; margin-top:70px; ">PRODUCT DESCRIPTION</h2>
    <form @submit.prevent="placeOrder">
    <div class="row">
      <div class="col">
        <div style="display: none;">
          <p>Image:</p>
          <img :src="productData.image" alt="Product Image" class="img-fluid" style="max-width: 200px; max-height: 200px;">
          <p>Product Name: {{ productData.prod_name }}</p>
          <p>Unit Price: ₱{{ productData.unit_price }}</p>
          <p>size : {{ getSizeName(productData.size_id) }}</p>
      </div>
        <div class="mb-3">
          <p>Quantity:</p>
          <div class="btn-group" role="group" aria-label="Quantity">
            <button @click="decreaseQuantity" type="button" class="btn btn-secondary" style="height:38px;">-</button>
            <p class="btn btn-secondary">{{ quantity }}</p>
            <button @click="increaseQuantity" type="button" class="btn btn-secondary" style="height:38px;">+</button>
          </div>
          </div>
          <!--hidden fields-->
          <input type="hidden" v-model="productData.id">
          <input type="hidden" v-model="productData.transaction_code">

          <div>
            <h5 >CONTACT DETAILS</h5>
            <div style="margin-top: 20px;">
              <div v-for="userData in info" :key="userData.id" class="user-profile">
                <div class="profile-details">
                  <div class="row">
                    <div class="col-sm-6">
                        <input type="text" v-model="userData.address" placeholder="Enter Address" required>
                    </div>
                    <div class="col-sm-6">
                        <input style="margin-left:90px;" type="text" v-model="userData.contact" placeholder="Enter Contact" required>
                    </div>
                  </div>
                  <div class="row">
                    <div class="col-sm-6">
                      <input type="text" v-model="userData.other_info" placeholder="Enter Other Information" required>
                      <input type="hidden" v-model="userData.token" placeholder="Enter Other Information" required>
                  </div>
                  <div class="col-sm-6">
                                        <!-- <label for="customerName">Customer Name:</label> -->
                    <input style="margin-left:90px;"  type="text" id="customerName" v-model="userData.username" placeholder="customer name" required>
                </div>
                  </div>
                  
                   <div>

                    <a style="position: absolute; margin-left:120px; margin-top:8px;">Total Price: ₱</a>
                    <input type="text" style="border:0px;  position: absolute;margin-left:192px;" v-model="productData.total" placeholder="total price" disabled> 
                  </div>
                </div>
              </div>
              
              <div class="payment-method" style="margin-top:40px; ">
                <label>Select Payment Method:</label><br>

                <input type="radio" id="paymaya" value="paymaya" v-model="selectedPaymentMethod">
                <label for="paymaya">
                  <img :src="require('../../../public/img/maya.png')" alt="PayMaya" style="width: 200px; height: 100px;">
                </label><br>
              
                <input type="radio" id="gcash" value="gcash" v-model="selectedPaymentMethod">
                <label for="gcash">
                  <img :src="require('../../../public/img/gcash.png')" alt="GCash" style="width: 200px; height: 200px;">
                </label><br>
              
              
                <input type="radio" id="cod" value="cod" v-model="selectedPaymentMethod">
                <label for="cod">Cash on Delivery</label>
              </div>


            </div>
          </div>


          <button @click="placeOrder('cart')" type="submit" class="btn btn-success">
            Add to Cart
          </button>
          <button @click="placeOrder('pending')" type="submit" class="btn btn-success">
            Place Order
          </button>
          
        </div>
      </div>
    </form>
    </div>
    
     <v-dialog v-model="dialog" persistent max-width="400">
      <v-card>
        <v-card-text>
          <div class="text-center">
            <p>  Your order has been successfully placed. </p>
            <img :src="require('../../../public/img/check.gif')"  style="width: 120px; height: 120px;">
          </div>
        </v-card-text>
        <v-card-actions>
          <v-btn  style="margin-left:162px;"  href="/userproducts" color="primary" @click="dialog = false">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>




</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      status: '',
      dialog: false,
      productData: {
        image: '',
        prod_name: '',
        unit_price: '',
        sizes: [],
        transaction_code: '',
        total: 0,

      },
      info: [],
      quantity: 1, // Initial quantity
      size_id: '',
      image:'', 
        prod_name:'', 
        unit_price:'',  
        address:'', 
        contact:'', 
        other_info:'', 
        customerName: '',
        id: '',
        token: '',
        status: '',
        username: '',
         
    };
  },
  
  created() {
    this.getProductDetails();
    this.token = sessionStorage.getItem('jwt');
    if (this.token) {
      this.getInfo();
    } else {
      console.error('JWT token not found in session storage');
    }
  },
  computed: {
    // Calculate total price based on quantity and unit price
    totalAmount() {
      return this.quantity * this.productData.unit_price;
    }
  },
  watch: {
    // Watch for changes in quantity or unit price to update total
    totalAmount() {
      this.productData.total = this.totalAmount;
    },
    'productData.unit_price'() {
      this.productData.total = this.totalAmount;
    }
  },
  methods: {
    async placeOrder(status) {
      try {
        const ins = await axios.post("placeOrder", {
          image: this.productData.image,
          prod_name: this.productData.prod_name,
          unit_price: this.productData.unit_price,
          size_id: this.productData.size_id,
          quantity: this.quantity,
          address: this.info[0].address, // Assuming you want the first user's address
          contact: this.info[0].contact, // Assuming you want the first user's contact
          other_info: this.info[0].other_info, // Assuming you want the first user's other info
          token: this.info[0].token, // Assuming you want the first user's other info
          customerName: this.info[0].username,
          id: this.productData.id,
          transaction_code: this.productData.transaction_code,
          total: this.productData.total,
          status: status,
          
        });

        // Resetting data after order placement
        this.quantity = 1;
        this.customerName = "";

        // Triggering data retrieval after order placement
        await this.getInfo();

        this.dialog = true;
      } catch (error) {
        console.error(error);
      }
    },
    async getInfo() {
      try {
        const response = await axios.get(`getUserData/${this.token}`);
        this.info = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    getProductDetails() {
      this.productData.image = this.$route.params.image || '';
      this.productData.prod_name = this.$route.params.prod_name || '';
      this.productData.unit_price = this.$route.params.unit_price || 0;
      this.productData.size_id = this.$route.params.size_id || '';
      this.productData.stock = this.$route.params.stock || 0;
      this.productData.id = this.$route.params.id || '';
      this.productData.transaction_code = this.$route.params.transaction_code || '';
      
    },
    increaseQuantity() {
  if (this.quantity < this.productData.stock) {
    this.quantity += 1;
  } else {
    console.log('Maximum stock reached');
  }
},
    decreaseQuantity() {
      if (this.quantity > 1) {
        this.quantity -= 1;
      }
    },
    async fetchSizes() {
      try {
        const response = await axios.get("getsize");
        this.productData.sizes = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    getSizeName(sizeId) {
  const size = this.productData.sizes.find(size => size.size_id === sizeId);
  return size ? size.item_size : 'Unknown';
},
  },
  mounted() {
    this.fetchSizes();
    this.getInfo();
  },
};
</script>

<style>
@import url('https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css');
/* Add additional styles if needed */
.user-profile {
  width: 220px;
  margin-right: 100px;
}
</style>
