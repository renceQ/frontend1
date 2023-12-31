<template>
    <div style="margin-right: 500px;">
      <div>
        <br>
        <h4 style="margin-left:550px">Register New Account</h4>
        <h5 style="margin-left: 630px;"></h5>
        <v-sheet>
          <v-form @submit.prevent="register">
            <div v-if="message" class="error-message-box">
              <div class="error-message">{{ message }}</div>
            </div>
<br>
  
            <v-text-field v-model="username" label="Username" type="username" outlined dense required></v-text-field>
            <v-text-field v-model="password" label="Password" type="password" outlined dense required></v-text-field>
            <v-text-field v-model="passwordConfirm" label="Password Confirm" type="password" outlined dense required></v-text-field>
  
            <v-btn type="submit" block class="mt-2" color="#000000">Submit</v-btn>
            <router-link to="/" style="color: #FFFFCC;" class="d-block text-center mt-2">Login</router-link>
          </v-form>
        </v-sheet>
      </div>
    </div>
  </template>
  
  <script>
  import router from '@/router';
  import axios from 'axios';
  
  export default {
    data() {
      return {
        username: '',
        password: '',
        passwordConfirm: '',
        message: '', // Initially, no message
      };
    },
    methods: {
      async register() {
  this.message = ''; // Resetting message before each form submission

  if (!this.username || !this.password || !this.passwordConfirm) {
    this.message = 'Please fill in all fields';
    return;
  }

  // Validate password complexity
  const letterNumberRegex = /^(?=.*[a-zA-Z])(?=.*[0-9])/;
  if (this.password.length < 10 || !letterNumberRegex.test(this.password)) {
    this.message = 'Password must be at least 10 characters long and contain both letters and numbers.';
    return;
  }

  if (this.password !== this.passwordConfirm) {
    this.message = 'Passwords do not match';
    return;
  }

  try {
    // Check if username already exists
    const usernameExists = await this.checkUsernameExists(this.username);
    
    if (usernameExists) {
      this.message = 'Username already exists';
      return;
    }

    // If username does not exist, proceed with registration
    const data = await axios.post('api/register', {
      username: this.username,
      password: this.password,
    });

    if (data.data.msg === 'okay') {
      alert('Registered successfully');
      router.push('/');
    } else {
      this.message = data.data.msg || 'Invalid response';
    }
  } catch (error) {
    console.error('Error:', error);
    this.message = 'Error occurred while registering';
  }
},

async checkUsernameExists(username) {
  try {
    const response = await axios.post('api/checkUsername', {
      username: username,
    });
    
    return response.data.exists; // Return true or false based on server response
  } catch (error) {
    console.error('Error checking username:', error);
    return false; // Return false in case of an error
  }
},
    },
  };
  </script>

  <style>
  .error-message-box {
    background-color: rgba(255, 0, 0, 0.05);
    padding: 8px;
    border-radius: 8px;
  }
  
  .error-message {
    color: rgb(60, 60, 60);
    font-size: 14px;
  }

  </style>
  