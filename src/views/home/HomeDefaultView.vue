<template>
	<div class="container">
		Y este random quien es???
		<img src="https://i.redd.it/iyfb41n8hv171.jpg" alt="">
	</div>
</template>

<script setup>
	import { ref } from 'vue';
  	import { useRouter } from 'vue-router';
	import { isAdminLoggedIn, isEmployeeLoggedIn, isClientLoggedIn } from '@/router/index.js';

	const router = useRouter()
	if(isClientLoggedIn.value == true)
		router.push('/client');
	else if(isEmployeeLoggedIn.value == true)
		router.push('/employee');
	else if(isAdminLoggedIn.value == true)
		router.push('/plans');
</script>

