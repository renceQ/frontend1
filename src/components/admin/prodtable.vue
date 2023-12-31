<template>
  
 
  <div class="row" style="margin-left: 300px; margin-right: 20px;">
    <div class="text-right mt-3" style="margin-top: 100px;">
      <button @click="previousPage" :disabled="currentPage === 1" class="btn btn-outline-primary mr-2">
        <i class="fa fa-chevron-left"></i>
      </button>
      <span>{{ currentPage }}</span>
      <button @click="nextPage" :disabled="currentPage * pageSize >= info.length" class="btn btn-outline-primary ml-2">
        <i class="fa fa-chevron-right"></i>
      </button>
    </div>
    <div class="col-md-12 col-sm-12">
          <h2>List of Products</h2>
        </div>
        <div class="container">
          <div class="row">
            <button @click="openModal" style=" width:18%; margin-left:78%; margin-bottom:3%;" class="neumorphic-button">Add Product</button>
                <div class="card-body">
                  <insert @data-saved="getInfo" />
                  <div class="table-container">
                    <table id="datatable-responsive">
                      <thead class="thead-dark sticky-header">
                        <tr>
                          <th>UPC</th>
                          <th>Category</th>
                          <th>Item Image</th>
                          <th>Product</th>
                          <th>Status</th>
                          <th>Stock</th>
                          <th>Price</th>
                          <th>Size</th>
                          <th>Unit Price</th>
                          <th>Action</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="product in paginatedData" :key="product.id">
                          <td>{{ product.UPC }}</td>

                          <td>{{ getCategoryName(product.category_id) }}</td>
                          <td v-if="product.image">
                            <img :src="product.image" alt="image" class="img-fluid" style="max-width: 100px; max-height:100px;">
                          </td>
                          <td>{{ product.prod_name }}</td>
                          <td>
                            <span v-if="product.stock <= 15" class="text-danger">Low Stock</span>
                            <span v-else class="text-success">High Stock</span>
                          </td>
                          <td>{{ product.stock }}</td>
                          <td>{{ product.price }}</td>
                          <td>{{ getSizeName(product.size_id) }}</td>
                          <td>{{ product.unit_price }}</td>
                          <td>
                            <td>
                              <div>
                              <btn @click="openEditModal(product.id)" class="neumorphic-edit">
                                <i class="fa fa-edit"></i> EDIT
                              </btn>
                            </div>
                            <br>
                              <div>
                                <router-link :to="{ name: 'AudithTable', params: { productId: product.id } }" class="neumorphic-edit">
                                  <i class="fas fa-clipboard-list" @click="openAuditModal(product.id)"></i> AUDIT
                                </router-link>                             
                            </div>  
                            <br>
                            <div>
                              <router-link :to="{ name: 'SalesTable', params: { productId: product.id } }" class="neumorphic-edit">
                                <i class="fas fa-chart-bar" @click="openAuditModal(product.id)"></i> SALES
                              </router-link>                             
                          </div>      
                            </td>
                          </td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>
              </div>
              </div>
        
        
 
          <v-dialog v-model="modalOpen" max-width="600" @close="clearModalFields">
            <v-card>
              <v-card-title>Add Product</v-card-title>
              <v-card-text>
                <v-container>
                  <!-- Category and Size Selection -->
                  <v-row>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="category_id">Category</label>
                        <select class="form-control form-control-sm" v-model="category_id" @change="updateCategory" required>
                          <option v-for="category in categories" :key="category.id" :value="category.id">
                            {{ category.category_name }}
                          </option>
                        </select>
                      </div>
                    </v-col>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="size_id">Size</label>
                        <select class="form-control form-control-sm" v-model="size_id" @change="updateSize" required>
                          <option v-for="size in sizes" :key="size.size_id" :value="size.size_id">
                            {{ size.item_size }}
                          </option>
                        </select>
                      </div>
                    </v-col>
                  </v-row>
          
                  <!-- Image Upload and Product Name -->
                  <v-row>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="image">Image</label>
                        <input type="file" class="form-control-file form-control-sm" accept="image/*" @change="handleImageUpload" required>
                        <img v-if="imageUrl" :src="imageUrl" alt="Selected Image" style="max-width: 200px; max-height: 200px;">
                      </div>
                    </v-col>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="prod_name">Product</label>
                        <input type="text" class="form-control form-control-sm" placeholder="Product Name" v-model="prod_name" required>
                      </div>
                    </v-col>
                  </v-row>
          
                  <!-- Stock and Unit Price -->
                  <v-row>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="stock">Stock</label>
                        <input type="number" class="form-control form-control-sm" placeholder="Stock" v-model="stock" @input="handleStockChange" required>
                      </div>
                    </v-col>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="unit_price">Unit Price</label>
                        <input type="number" class="form-control form-control-sm" placeholder="Unit Price" v-model="unit_price" @input="handleUnitPriceChange" required>
                      </div>
                    </v-col>
                  </v-row>
          
                  <!-- Total Price and UPC -->
                  <v-row>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="price">Total Price</label>
                        <input type="text" class="form-control form-control-sm" placeholder="Total Price" v-model="price" disabled>
                      </div>
                    </v-col>
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="UPC">UPC</label>
                        <input type="text" class="form-control form-control-sm" placeholder="UPC" v-model="UPC" disabled>
                      </div>
                    </v-col>
                  </v-row>
          
                  <!-- Submit Button -->
                  <v-row style="margin-left: 10px;">
                    Barcode:
                    <v-col cols="12">
                      <canvas id="upcCanvas"></canvas>
                    </v-col> 
                    <v-col cols="6">
                      <div class="form-group">
                        <label for="product_description">Product Description</label>
                        <input type="text" class="form-control form-control-sm" placeholder="productdescription" v-model="product_description" required>
                      </div>
                    </v-col>
                  </v-row>

                  <v-row>
                    <v-col cols="12">
                      <v-btn type="submit" color="primary" small @click="save">Submit</v-btn>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
            </v-card>
          </v-dialog>


          <!-- Success Modal -->
  <v-dialog v-model="successModalOpen" max-width="400">
    <v-card>
      <v-card-text>
        <div class="text-center">
          <p>Product added successfully</p>
          <img :src="require('../../../public/img/check.gif')"  style="width: 120px; height: 120px;">
        </div>
        <v-btn style="margin-left:145px;" color="primary" @click="closeSuccessModal">OK</v-btn>
      </v-card-text>
    </v-card>
  </v-dialog>




  <!--edit-->
  <v-dialog v-model="editmodal" max-width="600">
    <v-card>
      <v-card-title>Edit Product</v-card-title>
      <v-card-text>
        <v-container>
          <!-- Category and Size Selection -->
          <v-row>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_category_id">Category</label>
                <select class="form-control form-control-sm" v-model="edit_category_id" @change="updateCategory" required>
                  <option v-for="category in categories" :key="category.id" :value="category.id">
                    {{ category.category_name }}
                  </option>
                </select>
              </div>
            </v-col>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_size_id">Size</label>
                <select class="form-control form-control-sm" v-model="edit_size_id" @change="updateSize" required>
                  <option v-for="size in sizes" :key="size.size_id" :value="size.size_id">
                    {{ size.item_size }}
                  </option>
                </select>
              </div>
            </v-col>
          </v-row>
  
          <!-- Image Upload and Product Name -->
          <v-row>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_image">Image</label>
                <input type="file" class="form-control-file form-control-sm" accept="image/*" @change="updateEditImage">
              </div>
            </v-col>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_prod_name">Product</label>
                <input type="text" class="form-control form-control-sm" placeholder="Product Name" v-model="edit_prod_name" required>
              </div>
            </v-col>
          </v-row>
  
          <br>
                <a>Product Image:</a>
                <br>
               <img v-if="edit_image" :src="edit_image" alt="Selected Image" style="max-width: 200px; max-height: 200px;">
          <!-- Stock and Unit Price -->
          <v-row>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_stock">Stock</label>
                <input type="number" class="form-control form-control-sm" placeholder="Stock" v-model="edit_stock" @input="handleEditStockChange" required>
              </div>
            </v-col>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_unit_price">Unit Price</label>
                <input type="number" class="form-control form-control-sm" placeholder="Unit Price" v-model="edit_unit_price" @input="handleEditUnitPriceChange" required>
              </div>
            </v-col>
          </v-row>
  
          <!-- Total Price and UPC -->
          <v-row>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_price">Total Price</label>
                <input type="text" class="form-control form-control-sm" placeholder="Total Price" v-model="edit_price" disabled>
              </div>
            </v-col>
            <v-col cols="6">
              <div class="form-group">
                <label for="edit_UPC">UPC</label>
                <input type="text" class="form-control form-control-sm" placeholder="UPC" v-model="edit_UPC" disabled>
              </div>
            </v-col>
          </v-row>
  
          <!-- Product Description -->
          <v-row>
              <v-col cols="12">
                Barcode:
                <canvas id="editUpcCanvas"></canvas>
              </v-col>
            <v-col cols="12">
              <div class="form-group">
                <label for="edit_product_description">Product Description</label>
                <input type="text" class="form-control form-control-sm" placeholder="Product Description" v-model="edit_product_description" required>
              </div>
            </v-col>
          </v-row>
  
          <!-- Submit Button -->
          <v-row>
            <v-col cols="12">
              <v-btn type="submit" color="primary" small @click="updateProduct">Update</v-btn>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
    </v-card>
  </v-dialog>



         

</template>

<script>
import axios from 'axios';
import JsBarcode from 'jsbarcode'; 
import { sha256 } from 'js-sha256';

export default {
  data() {
    return {
      info: [],
      categories: [],
      sizes: [],
      pageSize: 10, // Number of items per page
      currentPage: 1, // Current page number
      modalOpen: false, 
      successModalOpen: false,
    

      category_id: "",
      size_id: "",
      image: null,
      prod_name: "",
      stock: "",
      price: "",
      unit_price: "",
      UPC: '',
      product_description: '',
      imageUrl: null, 
      prods: [],

      //edit
      editmodal: false,
    edit_category_id: "",
    edit_size_id: "",
    edit_image: null,
    edit_prod_name: "",
    edit_stock: "",
    edit_price: "",
    edit_unit_price: "",
    edit_UPC: "",
    edit_product_description: "",
    editImageUrl: null,

    editBarcodeImage: null,
      selectedProductId: null,

    };
  },
  computed: 
    {
    paginatedData() {
      const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      return this.info.slice(startIndex, endIndex);
    },
  },
  shouldGenerateBarcode() {
      return (
        this.category_id &&
        this.size_id &&
        this.prod_name &&
        this.unit_price &&
        this.stock
      );
    },
  watch: {
    shouldGenerateBarcode() {
      this.generateUPC();
    },
    category_id() {
      this.generateUPC();
    },
    size_id() {
      this.generateUPC();
    },
    prod_name() {
      this.generateUPC();
    },
    unit_price() {
      this.generateUPC();
    },
    stock() {
      this.generateUPC();
    },
  },
  created() {
    this.refreshData();
    this.getData();
    this.getInfo();
  },
  methods: {
    
    async openAuditModal(productId) {
    try {
      const response = await axios.get(`getaudith/${productId}`);
      this.products = response.data;
    } catch (error) {
      console.error(error);
    }
  },
    handleEditStockChange() {
      this.calculateEditTotalPrice(); // Calculate total price
      this.generateUPCForEdit(); // Regenerate UPC when stock changes
    },

    handleEditUnitPriceChange() {
      this.calculateEditTotalPrice(); // Calculate total price
      this.generateUPCForEdit(); // Regenerate UPC when unit price changes
    },
  calculateEditTotalPrice() {
    if (this.edit_stock && this.edit_unit_price) {
      this.edit_price = (parseInt(this.edit_stock) * parseInt(this.edit_unit_price)).toString();
    } else {
      this.edit_price = "";
    }
  },
 
async updateProduct() {
  try {
    const response = await axios.post(`/updateItem/${this.selectedProductId}`, {
      edit_category_id: this.edit_category_id,
      edit_size_id: this.edit_size_id,
      edit_image: this.edit_image, // Handle image uploading if necessary
      edit_prod_name: this.edit_prod_name,
      edit_stock: this.edit_stock,
      edit_price: this.edit_price,
      edit_unit_price: this.edit_unit_price,
      edit_UPC: this.edit_UPC,
      edit_product_description: this.edit_product_description,
    });

    if (response.status === 200) {
      // Handle success, close the edit modal or show a success message
      this.editmodal = false; // Close the edit modal
      this.successModalOpen = true; // Show success modal or message
      // Reset the form fields or update the product list
      this.refreshData(); // Function to refresh product list after update
      this.getInfo();
    } else {
      // Handle error cases, show error message or handle as per your requirement
      console.error('Error updating product');
    }
  } catch (error) {
    // Handle network errors or other exceptions
    console.error('Error updating product:', error);
  }
},

    openEditModal(productId) {
      // Store the selected product ID in the data property
      this.selectedProductId = productId;

      // Find the product details using its ID
      const selectedProduct = this.info.find(product => product.id === productId);

      // Populate the fields in the edit modal with the selected product's data
      this.edit_category_id = selectedProduct.category_id;
      this.edit_size_id = selectedProduct.size_id;
      this.edit_image = selectedProduct.image;
      this.edit_prod_name = selectedProduct.prod_name;
      this.edit_stock = selectedProduct.stock;
      this.edit_price = selectedProduct.price;
      this.edit_unit_price = selectedProduct.unit_price;
      this.edit_UPC = selectedProduct.UPC;
      this.edit_product_description = selectedProduct.product_description;

      // Open the edit modal
      this.editmodal = true;
    },
    openModal() {
      this.modalOpen = true; // Open the modal
    },
    closeModal() {
      this.modalOpen = false; // Close the modal
    },
    clearModalFields() {
      this.category_id = "";
      this.size_id = "";
      this.image = null;
      this.prod_name = "";
      this.stock = "";
      this.price = "";
      this.unit_price = "";
      this.UPC = "";
      this.product_description = "";
    },
    openSuccessModal() {
      this.successModalOpen = true;
    },

    // Method to close success modal
    closeSuccessModal() {
      this.successModalOpen = false;
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
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    nextPage() {
      const maxPage = Math.ceil(this.info.length / this.pageSize);
      if (this.currentPage < maxPage) {
        this.currentPage++;
      }
    },
    handleStockChange() {
    this.calculateTotalPrice();
    this.generateUPC();
  },

  handleUnitPriceChange() {
    this.calculateTotalPrice();
    this.generateUPC();
  },
    calculateTotalPrice() {
      if (this.stock && this.unit_price) {
        this.price = (parseInt(this.stock) * parseInt(this.unit_price)).toString();
      } else {
        this.price = "";
      }
    },
    generateUPC() {
      const uniqueIdentifier = `${this.category_id}${this.size_id}${this.prod_name}${this.unit_price}`;
      const hashedUPC = sha256(uniqueIdentifier);
      const numericUPC = hashedUPC.replace(/\D/g, '').substring(0, 12);

      this.UPC = numericUPC;

      JsBarcode("#upcCanvas", numericUPC, {
        format: "EAN13",
        width: 2,
        height: 30,
        displayValue: false,
      });
    },
    generateUPCForEdit() {
      const uniqueIdentifier = `${this.edit_category_id}${this.edit_size_id}${this.edit_prod_name}${this.edit_unit_price}${this.edit_stock}`;
      const hashedUPC = sha256(uniqueIdentifier);
      const numericUPC = hashedUPC.replace(/\D/g, '').substring(0, 12);

      this.edit_UPC = numericUPC;

      JsBarcode("#editUpcCanvas", numericUPC, {
        format: "EAN13",
        width: 2,
        height: 30,
        displayValue: false,
      });

      // Convert the generated barcode to an image URL and bind it to the editBarcodeImage data property
      const canvas = document.getElementById('editUpcCanvas');
      this.editBarcodeImage = canvas.toDataURL("image/png");
    },
    clearUPC() {
      this.UPC = '';
      const canvas = document.getElementById('upcCanvas');
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    },


    async save() {
  // Check if any required field is empty...
  if (
    !this.category_id ||
    !this.size_id ||
    !this.image ||
    !this.prod_name ||
    !this.stock ||
    !this.unit_price ||
    !this.UPC ||
    !this.product_description
  ) {
    // Prompt a message to fill out all required fields
    alert('Please fill out all required fields');
    return; // Prevent further execution of saving logic
  }

  try {
    // Rest of your save logic if all required fields are filled
    const formData = new FormData();
    formData.append("category_id", this.category_id);
    formData.append("size_id", this.size_id);
    formData.append("image", this.image);
    formData.append("prod_name", this.prod_name);
    formData.append("stock", this.stock);
    formData.append("price", this.price);
    formData.append("unit_price", this.unit_price);
    formData.append("UPC", this.UPC);
    formData.append("product_description", this.product_description);

    // Add barcode_image to formData
    formData.append("barcode_image", this.saveBarcodeImage());

    const product = await axios.post("save", formData);

    console.log("Product saved successfully:", product);

    // Clear form fields after successful save
    this.category_id = "";
    this.size_id = "";
    this.image = null;
    this.prod_name = "";
    this.stock = "";
    this.price = "";
    this.unit_price = "";
    this.UPC = "";
    this.product_description = "";

    // Close the modal after successful save
    this.modalOpen = false;

    // Show success modal
    this.openSuccessModal();

    // Refresh data if needed
    this.refreshData();
    this.getInfo();
  } catch (error) {
    console.error("Error saving product:", error);
  }
},



saveBarcodeImage() {
  const numericUPC = this.generateUPC();

  // Save barcode image to canvas
  JsBarcode("#upcCanvas", numericUPC, {
    format: "EAN13",
    width: 2,
    height: 30,
    displayValue: false,
  });

  // Convert canvas to image URL
  const canvas = document.getElementById('upcCanvas');
  const barcodeImageUrl = canvas.toDataURL("image/png");

  return barcodeImageUrl;
},
    async refreshData() {
      try {
        const response = await axios.get("getDatas");
        this.prods = response.data;

        this.prods.forEach(room => {
          console.log('Product Data:', room);
        });
      } catch (error) {
        console.error("Error fetching prods:", error);
      }
    },
    handleImageUpload(event) {
      this.image = event.target.files[0];
    },
    updateEditImage(event) {
    const file = event.target.files[0]; // Get the selected file
    if (file) {
      // Assuming you want to display the selected image in the edit modal
      const reader = new FileReader();
      reader.onload = (e) => {
        this.edit_image = e.target.result; // Set the edit_image data property to the image URL
      };
      reader.readAsDataURL(file); // Convert the file to a data URL
    } else {
      this.edit_image = null; // Reset edit_image if no file is selected
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
/* Additional or modified CSS styles for the table */
/* Additional or modified CSS styles for the table */
.sticky-header th {
  position: sticky;
  top: 0;
  background-color: #f2f2f2; /* Adjust the background color as needed */
  z-index: 100;
}

.table-container {
  max-height: 400px; /* Set a smaller maximum height for the table container */
  overflow-y: auto; /* Enable vertical scrolling */
}

.table-wrapper {
  overflow: hidden; /* Hide table overflow beyond the container */
}

.table-responsive {
  width: fit-content; /* Set width to fit the content */
  min-width: 100%; /* Set minimum width to ensure the table fills the container */
}

#datatable-responsive {
  width: 100%; /* Ensure table takes full width */
  border-collapse: collapse;
  table-layout: fixed; /* Enforce fixed table layout */
}

#datatable-responsive th,
#datatable-responsive td {
  border: 1px solid #ddd;
  padding: 8px;
  white-space: nowrap; /* Prevent text wrapping */
  overflow: hidden; /* Hide overflowing content */
  text-overflow: ellipsis; /* Show ellipsis for overflowing text */
}

#datatable-responsive th {
  background-color: #f2f2f2;
  color: #333;
  text-align: left;
}

#datatable-responsive tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

#datatable-responsive tbody tr:hover {
  background-color: #e6e6e6;
}

.fixed-header-table {
  width: 100%;
  border-collapse: collapse;
  table-layout: fixed;
}

.fixed-header-table thead th {
  position: sticky;
  top: 0;
  z-index: 1;
  background-color: #f2f2f2;
  color: #333;
  text-align: left;
}
.neumorphic-button {
  width: 80px;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  background-color: #f0f0f0;
  color: #444;
  box-shadow: 3px 3px 5px #b8b8b8, -3px -3px 5px #ffffff;
  transition: all 0.3s ease;
}

.neumorphic-button:hover {
  background-color: #e0e0e0;
}

.neumorphic-edit {
  padding: 4px 10px;
  border-radius: 8px;
  background-color: #f0f0f0;
  color: #444;
  box-shadow: 3px 3px 5px #b8b8b8, -3px -3px 5px #ffffff;
  transition: all 0.3s ease;
  text-decoration: none;
}

.neumorphic-edit:hover {
  background-color: #e0e0e0;
  color: #000; /* Change text color on hover if needed */
}

</style>