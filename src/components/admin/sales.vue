<template>
    <div class="table-container">
        <h1>Sales ID: {{ productId }}</h1>
        <insert @data-saved="getSalesRecord" />
        <table class="product-table">
        <thead>
          <tr>
            <th>Image</th>
            <th>Product Name</th>
            <th>Unit Price</th>
            <th>Total Price</th>
            <th>Size ID</th>
            <th>Quantity</th>
            <th>Address</th>
            <th>Contact</th>
            <th>Other Info</th>
            <th>Customer Name</th>
            <th>Status</th>
            <th>Transaction Code</th>
            <th>Type</th>
            
          </tr>
        </thead>
        <tbody>
            <tr v-for="salesRecord in salesRecords" :key="salesRecord.id">
            <td v-if="salesRecord.image">
                <img :src="salesRecord.image" alt="image" class="img-fluid" style="max-width: 100px; max-height:100px;">
              </td>
            <td>{{ salesRecord.prod_name }}</td>
            <td>{{ salesRecord.unit_price }}</td>
            <td>{{ salesRecord.total }}</td>
            <td>{{ getSizeName(salesRecord.size_id) }}</td>
            <td>{{ salesRecord.quantity }}</td>
            <td>{{ salesRecord.address }}</td>
            <td>{{ salesRecord.contact }}</td>
            <td>{{ salesRecord.other_info }}</td>
            <td>{{ salesRecord.customerName }}</td>
            <td>{{ salesRecord.status }}</td>
            <td>{{ salesRecord.transaction_code }}</td>
            <td>{{ salesRecord.type }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>

  <script>
import axios from 'axios'

export default {
  data() {
    return {
        id: "",
        image: null,
        quantity: "",
        prod_name: "",
        stock: "",
        address: "",
        unit_price: "",
        size_id: "",
        contact: "",
        other_info: "",
        customerName: "",
        status: "",
        product_id: "",
        old_stock:"",
        transaction_code: "",
        type: "",
        total: "",
      products: [],
      categories: [],
      sizes: [],
      selectedProductId: null,
      productId: '',
      salesRecords: [],
      }
  },
  created() {
   
    this.productId = this.$route.params.productId; // Get the productId from the route

if (this.productId) {
  this.getSalesRecord(this.productId); // Fetch audit records based on productId
}
  },
  methods: {
    async getSalesRecord(productId) {
      try {
        const response = await axios.get(`getsales/${productId}`);
        this.salesRecords = response.data; // Update salesRecords with fetched data
      } catch (error) {
        console.error(error);
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
    },async fetchCategories() {
      try {
        const response = await axios.get("getcat");
        this.categories = response.data; // Assuming response.data contains the categories array
      } catch (error) {
        console.error(error);
      }
    },
    async fetchsizes() {
      try {
        const response = await axios.get("getsize");
        this.sizes = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async fetchsizes() {
      try {
        const response = await axios.get("getsize");
        this.sizes = response.data;
      } catch (error) {
        console.error(error);
      }
    },
  },
  
   
    mounted() {
    this.fetchCategories();
    this.fetchsizes();
  },
};
</script>
  
<style>
/* Add styles to the table container */
.table-container {
  width: 1000px;
  margin-right: 40px;
  font-size: 12px; /* Adjust the font size as needed */
}

/* Add styles to the table */
.product-table {
  width: 100%;
  border-collapse: collapse;
  font-family: Arial, sans-serif;
}

.product-table th,
.product-table td {
  border: 1px solid #ddd;
  padding: 6px; /* Adjust cell padding */
  text-align: left;
}

.product-table th {
  background-color: #f2f2f2;
  font-weight: bold;
}

.product-table tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

.product-table tbody tr:hover {
  background-color: #e9e9e9;
}

/* Align the table to the right */
@media (min-width: 768px) {
  .table-container {
    float: right;
    margin-left: 20px; /* Adjust margin as needed */
  }
}
</style>