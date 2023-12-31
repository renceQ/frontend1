<template>
  <div class="container">
    <br><br><br><br><br><br><br>
    <h1 class="text-center" style="font-size: 50px;">Available <span>Products...</span></h1>
    <br><br>

    <div class="row justify-content-center">
      <div class="col-md-6">
        <label for="category_id" class="label text-center">Category</label>
        <div class="select-wrapper" style="margin-left:180px;" >
          <select v-model="category_id" @change="filterProducts" class="select">
            <option value="">All Categories</option>
            <option v-for="category in categories" :key="category.id" :value="category.id">
              {{ category.category_name }}
            </option>
          </select>
          <i class="fas fa-caret-down arrow-icon"></i>
        </div>
      </div>
    </div>
    <br>

    <!-- Show products -->
    <div class="row justify-content-center">
      <div v-for="(product, index) in info" :key="product.id" class="col-lg-3 col-md-6 mb-4">
        <!-- Product Card -->
        <div class="room-item text-center">
          <img :src="product.image" alt="" style="width: 180px; height: 180px;">
          <div class="ri-text">
            <h4>{{ product.prod_name }}</h4>
            <p>Unit Price: ₱{{ product.unit_price }}</p>
            <p>Available Size: {{ getSizeName(product.size_id) }}</p>
            <button class="btn btn-outline-danger btn-sm" @click="preOrder(product)">Pre order</button>
          </div>
        </div>
      </div>
    </div>
  </div>

   <!-- generated transact code -->
  <input type="hidden" v-model="productData.transaction_code">

    <!-- Footer Section -->
    <footer class="ftco-footer ftco-bg-dark ftco-section">
      <div class="container">
        <div class="row mb-5">
          <div class="col-md">
            <div class="ftco-footer-widget mb-4">
              <h2 class="ftco-heading-2">QMJ IMAGES ENTERPRISES</h2>
              <p>Your best source of Photography and Videography
Sound and stage lights production.</p>
              <ul class="ftco-footer-social list-unstyled float-md-left float-lft mt-5">
                <li class="ftco-animate"><a href="#"><span class="icon-twitter"></span></a></li>
                <li class="ftco-animate"><a href="#"><span class="icon-facebook"></span></a></li>
                <li class="ftco-animate"><a href="#"><span class="icon-instagram"></span></a></li>
              </ul>
            </div>
          </div>
          <div class="col-md">
            <div class="ftco-footer-widget mb-4 ml-md-5">
              <h2 class="ftco-heading-2">Useful Links</h2>
              <ul class="list-unstyled">
                <li><a href="#" class="py-2 d-block">Speakers</a></li>
                <li><a href="#" class="py-2 d-block">Shcedule</a></li>
                <li><a href="#" class="py-2 d-block">Events</a></li>
                <li><a href="/userblog" class="py-2 d-block">Blog</a></li>
              </ul>
            </div>
          </div>
          <div class="col-md">
             <div class="ftco-footer-widget mb-4">
              <h2 class="ftco-heading-2">Privacy</h2>
              <ul class="list-unstyled">
                <li><a href="/bookevents" class="py-2 d-block">Book Event</a></li>
                <li><a href="/about" class="py-2 d-block">About Us</a></li>
                <li><a href="/contacts" class="py-2 d-block">Contact Us</a></li>
                <li><a href="/userServices" class="py-2 d-block">Services</a></li>
                <li><a href="/userproducts" class="py-2 d-block">Products</a></li>
              </ul>
            </div>
          </div>
          <div class="col-md">
            <div class="ftco-footer-widget mb-4">
              <h2 class="ftco-heading-2">Want more Answers to your Questions?</h2>
              <div class="block-23 mb-3">
                <ul>
                  <li><span class="icon icon-map-marker"></span><span class="text">km. 13 BIGA, Calapan, Philippines</span></li>
                  <li><a href="Tel:0947 406 2928"><span class="icon icon-phone"></span><span class="text">0947 406 2928</span></a></li>
                  <li><a href="mailto:qmjimages2018@gmail.com"><span class="icon icon-envelope"></span><span class="text">qmjimages2018@gmail.com</span></a></li>
                  
                </ul>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 text-center">
  
          </div>
        </div>
      </div>
    </footer>
  </template>
  

<!-- Your existing script and style sections remain unchanged -->



 <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {  
        categories: [], 
        info: [],
      sizes: [],
      category_id: '',
      size_id: '',
      productData: { // Initialize productData object
      transaction_code: '', // Initialize transaction_code property
    }
      };
    },
    created() {
    this.getData();
  },
    methods: {

      generateTransactionCode() {
    // Generate a random transaction code (for example purposes)
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    const length = 8;
    let transactionCode = '';
    for (let i = 0; i < length; i++) {
      const randomIndex = Math.floor(Math.random() * chars.length);
      transactionCode += chars.charAt(randomIndex);
    }
    return transactionCode;
  },
   
      preOrder(product) {
  const { image, prod_name, unit_price, size_id, stock, id,   } = product;
  const transactionCode = this.generateTransactionCode();
  this.$router.push({
    name: 'productrequest',
    params: {
      image,
      prod_name,
      unit_price,
      size_id,
      stock, // Include stock parameter
      id,
      transaction_code: transactionCode,
    }
  });
},


      
      async filterProducts() {
      try {
        if (this.category_id) {
          const response = await axios.get(`getProductsByCategory/${this.category_id}`);
          this.info = response.data;
        } else {
          this.getInfo(); // Fetch all products if no category selected
        }
      } catch (error) {
        console.error(error);
      }
    },
      async fetchCategories() {
        try {
          const response = await axios.get("getcat");
          this.categories = response.data; // Assuming response.data contains the categories array
        } catch (error) {
          console.error(error);
        }
      },
      async getData() {
      try {
        await Promise.all([this.getCategories(), this.getInfo(), this.getItemSizes()]);
      } catch (error) {
        console.error(error);
      }
    },
    async getInfo() {
      try {
        const response = await axios.get('getDatas');
        this.info = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    async getCategories() {
      try {
        const response = await axios.get('getcat');
        this.categories = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    async getItemSizes() {
      try {
        const response = await axios.get('getsize');
        this.sizes = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    getSizeName(sizeId) {
      const size = this.sizes.find(size => size.size_id === sizeId);
      return size ? size.item_size : 'Unknown';
    },
    getCategoryName(categoryId) {
      const category = this.categories.find(category => category.id === categoryId);
      return category ? category.category_name : 'Unknown';
    },
      
    },
    mounted() {
      this.fetchCategories(); // Fetch categories when the component is mounted      not finished yet insert products
    },
  };
  </script>






<style>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css');

/* Style for the label */
.label {
  font-weight: bold;
  margin-bottom: 5px;
  display: block;
}

/* Style for the select dropdown */
.select-wrapper {
  position: relative;
  width: 200px;
}

.select {
  padding: 8px 30px 8px 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
  width: 100%;
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23333333"><path d="M7 10l5 5 5-5z" /></svg>') no-repeat right 8px center/12px;
}

.arrow-icon {
  position: absolute;
  top: 50%;
  right: 10px;
  transform: translateY(-50%);
  color: #333333;
  pointer-events: none;
}

</style>