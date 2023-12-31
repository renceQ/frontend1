<template>
	
	<form @data-saved="getUserDetails" />
	<div>
		<tr v-for="info in info">
			<td>{{ info.category_name}}</td>
			<td>
			  <button @click="editprofile" class="btn btn-success btn-sm edit">Edit profile</button>
			</td>
		  </tr>
	</div>
	<br>
	<br>
	<br>
	<br>
	<br>
    <div style="display: flex; align-items: center;">
		<img :src="require('../../assets/images/logo.jpg')" alt="Description of the image" style="width: 300px; margin-left: 200px;margin-right: 90px; margin-top: 90px;">
		<div style="margin-left:100px;">
			<br>
			<br>
			<br>
	
			<h1 class="navbar-brand" href="index.html" style="font-size: 60px; "> Wel<span>Come...</span></h1>
			<br>
			<h2 class="navbar-brand" href="index.html" style="font-size: 20px;">
				"Welcome to our page! where your convenience 
				<br>meets our expertise.Schedule your tailored<br>
				 service appointment today for a seamless <br>
			and personalized experience."
			</h2>
			<div>
				<router-link
  to="/about"
  class="neumorphic-link"
  style="
    padding: 10px 20px;
    font-size: 16px;
    background-color: #ffffff;
    border: none;
    border-radius: 4px;
    box-shadow: 4px 4px 8px #c7c7c7, -4px -4px 8px #ffffff;
    transition: all 0.3s ease;
    text-decoration: none; /* Remove underline */
    display: inline-block; /* To remove underline */
    position: relative; /* For pseudo-element */
  "
>
  Explore
</router-link>

			</div>
			<br>
		
		</div>
	</div>
</template>

<style>
@import url('https://fonts.googleapis.com/css?family=Work+Sans:100,200,300,400,500,600,700,800,900');
@import '../../assets/css/open-iconic-bootstrap.min.css';
@import '../../assets/css/animate.css';

@import '../../assets/css/owl.theme.default.min.css';
@import '../../assets/css/magnific-popup.css';
@import '../../assets/css/aos.css';
@import '../../assets/css/ionicons.min.css';

@import '../../assets/css/flaticon.css';
@import '../../assets/css/icomoon.css';
@import '../../assets/css/style.css';
.neumorphic-link {
	/* Your existing styles */
	/* ... */
  
	/* Remove underline by default */
	text-decoration: none;
  }
  
  .neumorphic-link:hover {
	/* Style on hover */
	background-color: #ddd;
	box-shadow: 5px 5px 10px #bcbcbc, -5px -5px 10px #ffffff;
	color: #000000; /* Change text color on hover */
  }
  
  /* Hide the underline only on hover */
  .neumorphic-link::after {
	content: '';
	position: absolute;
	left: 0;
	right: 0;
	bottom: -5px; /* Adjust this value to fine-tune the positioning */
	height: 1px;
	background-color: transparent; /* Make the underline transparent */
	transition: all 0.3s ease;
  }
  
  .neumorphic-link:hover::after {
	bottom: -10px; /* Move the underline away on hover */
	background-color: transparent; /* Hide the underline on hover */
  }
</style>

<script>


</script>