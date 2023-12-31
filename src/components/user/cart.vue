<template>

    <div style="position: absolute; margin-top:160px; margin-left:90px; ">
      <img style="width:70px; height:70px; "  v-if="info.length > 0" :src="info[0].profile_picture" alt="Profile" class="profile-picture-navbar">
      <span v-if="info.length > 0">
        <a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{{ info[0].username }}</a><br>
        <a style="position:absolute; margin-top:30px; text-decoration: none; color: black;" href="#"><i class="fas fa-pencil-alt"></i>&nbsp;&nbsp;&nbsp; Edit Profile</a><br>
        <a style="position:absolute; margin-top:30px; text-decoration: none; color:rgb(0, 0, 0);" href="/orderhistory"><i class="fas fa-history custom-icon"></i>&nbsp;&nbsp;&nbsp; Order History</a><br>
        <a style="position:absolute; margin-top:30px; text-decoration: none; color: black;" href="/toship_main"> <i class="fas fa-shopping-bag"></i>&nbsp;&nbsp;&nbsp; My Purchase</a><br>
        <a style="position:absolute; margin-top:30px; text-decoration: none; color: darkorange;" href="/addtocart"> <i class="fas fa-shopping-cart custom-icon"></i>  &nbsp;&nbsp;&nbsp;Shopping Cart</a>
      </span>
    </div>
    <div>

        <div class="neumorphic-search" style="margin-top: 170px; margin-left:315px;">
            <input type="text" placeholder="Search Product by name..." class="search-input" style="border: 0px;"/>
            <button style="position:absolute; margin-left:602px; width:49px; height: 49px; " class="search-button">
                <i class="fas fa-search"></i>
              </button>
              <button style="position:absolute; margin-left:680px; width:49px; height: 49px;" href="#" class="search-button">
              <i class="fas fa-shopping-cart custom-icon"></i>
              </button>
             
             
          </div>
             
               
      
    
        <nav class="neumorphic-navbars" style="margin-top: 20px; width: 950px; height: 60px; margin-left: 315px; z-index: 10;">
          <!-- Replace these router-links or hrefs with methods that filter based on status -->
         

          <span class="nav-item">
            <a href="/pending_main" class="nav-link" style="font-weight:700; color:darkorange;" >Shopping Cart</a>
          </span>
          <!-- <span class="nav-item">
            <a href="/orderhistory" class="nav-link" style="font-weight:400; color:rgb(0, 0, 0); margin-right:350px;" >Order History</a>
          </span> -->

          <a style="margin-left: 190px; margin-right: 20px;" class="navbar-brand">Product | <span>Status.</span></a>
        </nav>
      
    </div>




                <!--products container-->
                <div>
                  <div v-for="filteredInfo in filteredInfos" :key="filteredInfo.id" class="container" style="margin-top: 20px;">
                    <nav class="neumorphic-navbars" style="width: 950px; margin-left: 200px; z-index: 10;">
                      <ul>
                        <li>
                          <br>
                          <div>
                            <asd><span style="color:rgb(13, 109, 19); margin-left:580px;"><i class="fas fa-box custom-icon"></i>
                              &nbsp;&nbsp;&nbsp;We will be packing your parcel soon...</span></asd>
                          </div>
            
                          <div style="margin-bottom: 20px;"> 
                            <an  v-if="filteredInfo.image">
                              <img :src="filteredInfo.image" class="img-fluids" style="max-width: 140px; max-height: 140px;" readonly>
                              <span style="margin-right: 140px; margin-left: 80px;">Product:{{ filteredInfo.prod_name }}</span> 
                              <span style="margin-right: 140px;">Quantity:{{ filteredInfo.quantity }}</span> 
                              <span >Total: ₱{{ filteredInfo.total }}</span>
                              <span v-if="!hideStatus" class="product-info">{{ status }}</span> 
                              <span v-if="!hideToken" class="product-info">{{ token }}</span>
                            </an>
                            <div>
                              <!-- <button @click="undo(filteredInfo.id)" class="neumorphic-button" style="width: 200px; "><i class="fas fa-undo custom-icon"></i>&nbsp;&nbsp;
                                Undo</button> &nbsp;&nbsp; -->
                              <button @click="openModal" class="neumorphic-button" style="margin-left:450px; width: 200px;"><i class="fas fa-phone custom-icon"></i> &nbsp;&nbsp;Contact Seller</button> &nbsp;&nbsp;
                              <!-- <button @click="buyagain(filteredInfo.id)" class="neumorphic-button" style="margin-left:450px; width: 200px;"><i class="fas fa-phone custom-icon"></i> &nbsp;&nbsp;Buy Again</button> &nbsp;&nbsp; -->
                              <button  @click="openDialog(filteredInfo)"  class="neumorphic-button" style="width: 200px; background-color:rgb(248, 53, 53); color:white;">
                                Cancel Order</button>
                            </div>
                          </div>
                        </li>
                      </ul>
                    </nav>
                  </div>
                </div>
                


                <!--cancel modal-->
                <v-dialog v-model="dialogs" max-width="500px">
                    <v-card>
                      <v-card-title class="headline" style="margin-left: 99px;">Reasons for Order Cancellation</v-card-title>
                      <v-card-text>
                        <v-radio-group v-model="selectedReason">
                          <v-radio v-for="(reason, index) in cancellationReasons" :key="index" :label="reason" :value="reason"></v-radio>
                        </v-radio-group>
                        <!-- Add a hidden input to store the order ID -->
                        <input type="hidden" v-model="selectedInfo.id" ref="orderId">
                      </v-card-text>
                      <v-card-actions>
                        <v-btn @click="closeDialog" color="primary">Cancel</v-btn>
                        <v-btn @click="submitReason" color="primary" small>
                          Submit
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
              
</template>
<script>
import axios from 'axios';

export default {
  data() {
    return {
        isNavbarHidden: false,
      lastScrollTop: 0,
      inputValue: '',
      isActive: false,
      isProcessing: false,
      infos: [],
	    info: [],
      token: '',
      status: '',
      dialogs: false,
      selectedReason: null,
      cancellationReasons: [
        'Need to change delivery address',
        'Seller is not responsive to my inquiries',
        'Others/Change of mind'
        // Add more reasons if needed
      ],
      hideStatus: false,
      selectedReason: null,
      selectedInfo: null,
      
    };
  },
  mounted() {
	this.getInfo(); 
  this.getOrder();
  },
  created() {
	this.token = sessionStorage.getItem('jwt');
  if (this.token) {
    this.getInfo();
  } else {
    // Handle the case where token is not available in local storage
    console.error('JWT token not found in local storage');
  }
  this.getInfo();
},
computed: {


  filteredInfos() {
    // Filter the 'infos' array based on the token in session storage and status equals 'Approved'
    return this.infos.filter(info => info.token === this.token && info.status === 'cart');
  }
},
  methods: {
    async buyagain(id) {
  try {
    const confirmed = window.confirm('Are you sure you want to discard this change?');

    if (confirmed) {
      const userInput = prompt('Type "okay" to confirm:');
      if (userInput && userInput.trim().toLowerCase() === 'okay') {
        const response = await axios.post(`/updateOrderStatus/${id}`, {
          status: 'pending',
          reason: 'no valid reason',
        });

        if (response.status === 200) {
          this.getOrder(); // Refresh orders after status update
        } else {
          console.error('Error updating order status');
        }
      } else {
        console.log('No changes were made.');
      }
    } else {
      console.log('No changes were made.');
    }
  } catch (error) {
    console.error('Error updating order status:', error);
  }
},



    async submitReason() {
      try {
        if (this.selectedReason && this.selectedInfo) {
          const response = await axios.post(`/updateOrderStatus/${this.selectedInfo.id}`, {
            status: 'cancelled',
            reason: this.selectedReason,
          });

          if (response.status === 200) {
            this.getOrder(); // Refresh orders after status update
            this.closeDialog(); // Close the dialog after submitting reason
          } else {
            console.error('Error updating order status');
          }
        } else {
          console.error('Please select an order and a reason.'); // Inform the user to select an order and a reason
        }
      } catch (error) {
        console.error('Error updating order status:', error);
      }
    },
  
    openDialog(selectedInfo) {
  this.selectedInfo = selectedInfo; // Store the selected order info
  this.dialogs = true; // Open the dialog
},
    closeDialog() {
      this.dialogs = false; // Close the dialog
    },
    async getOrder() {
  try {
    const response = await axios.get('getOrder');
    this.infos = response.data;
    // Set hideToken to true after fetching notifications
    this.hideToken = true;
  } catch (error) {
    console.error(error);
  }
},
    toggleDropdown() {
    const menu = document.querySelector('.menu-item');
    menu.classList.toggle('active');
  },
  async getInfo() {
    try {
      const response = await axios.get(`getUserData/${this.token}`);
      this.info = response.data; // Assuming response.data is an object/array of user data
    } catch (error) {
      console.error(error);
      // Handle the error case, such as showing a message to the user
    }
  },
  handleScroll() {
      const currentScroll = window.pageYOffset || document.documentElement.scrollTop;

      if (currentScroll <= 0) {
        // At the top of the page
        this.isNavbarHidden = false;
      } else {
        // Scrolled down
        this.isNavbarHidden = true;
      }

      this.lastScrollTop = currentScroll <= 0 ? 0 : currentScroll;
    },
    activateFinder() {
      this.isActive = true;
    },
    deactivateFinder() {
      if (this.inputValue.length === 0) {
        this.isActive = false;
      }
    },
    submitForm() {
      this.isProcessing = true;
      this.isActive = false;
      this.$refs.icon.classList.remove('active');
      this.$refs.icon.classList.add('processing');
      this.inputValue = ''; // Clear input value

      setTimeout(() => {
        this.isProcessing = false;
        this.$refs.icon.classList.remove('processing');
        this.inputValue = ''; // Clear input value again

        if (this.inputValue.length > 0) {
          this.isActive = true;
          this.$refs.icon.classList.add('active');
        }
      }, 1000);
    },
  }
};
</script>

<style>

.neumorphic-navbars {

  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 10px 10px 30px #eeecec, -10px -10px 30px #ffffff;
  transition: top 0.3s;
}
.neumorphic-navbars a {
  color: rgb(14, 13, 13)262;
  text-decoration: none;
  padding: 8px 15px;
  border-radius: 3px;
  transition: all 0.3s ease;
}

/* Navbar link hover styles */
.neumorphic-navbars a:hover {
  background-color: #e9e1e1;
  box-shadow: 5px 5px 10px #bcbcbc, -5px -5px 10px #ffffff;
  color: #1b1b1b;
}

a[href="#"] {
  text-decoration: none;
  color: black;
}

.neumorphic-search {
    display: flex;
    align-items: center;
    border-radius: 5px;
    background-color: #fff;
    box-shadow: 10px 10px 30px #eeecec, -10px -10px 30px #ffffff;
    padding: 8px;
    height: 40px;
    margin-top: 35px; /* Adjust this margin as needed */
    margin-left: 70px; /* Adjust this margin as needed */
    width: 600px;
  }

  .search-input {
    height: 36px;
    margin-top: 2px;
    border: none;
    outline: none;
    flex: 1;
    padding: 8px;
    border-radius: 5px;
    box-shadow: inset 2px 2px 5px #c9c9c9, inset -2px -2px 5px #ffffff;
  }

  .search-button {
    background-color: #fff;
    border: none;
    outline: none;
    cursor: pointer;
    padding: 8px;
    border-radius: 5px;
    margin-left: 8px;
    margin-bottom: 12px;
    width: 40px;
    box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.1);
  }
</style>
