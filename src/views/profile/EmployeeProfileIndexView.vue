<template>
	<form @submit.prevent="modificarPerfil">
		<div id="edit-profile-modal" class="fixed top-0 left-0 w-screen h-screen bg-black bg-opacity-30 hidden justify-center items-center z-20 modal" @click="closeAllModals">
			<div class="max-w-7xl rounded-lg bg-gray-100 p-10 flex flex-col gap-6" @click.stop="">
				<div class="flex justify-between items-center gap-3">
					<p class="text-xl font-semibold">Modificar datos personales</p>
					<button type="button" class="w-8 h-8 hover:bg-gray-200 rounded-full modal" @click.stop="closeModal('edit-profile-modal')">
						<i class="fa-solid fa-times text-lg"></i>
					</button>
				</div>
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4">
					<div class="flex flex-col gap-1">
						<label for="name">Nombres </label>
						<input type="text" id="namePerf" class="input-text" v-model="newUserInfo.nombre" required>
					</div>
					<div class="flex flex-col gap-1">
						<label for="lastname">Apellidos </label>
						<input type="text" id="lastnamePerf" class="input-text" v-model="newUserInfo.apellido" required>
					</div>
					<div class="flex flex-col gap-1">
						<label for="email">Correo electrónico </label>
						<input type="email" id="emailPerf" class="input-text" v-model="newUserInfo.email" required>
					</div>
					<div class="flex flex-col gap-1">
						<label for="phone">Teléfono </label>
						<input type="tel" id="phonePerf" class="input-text" v-model="newUserInfo.telefono" pattern="^\d{10}$" title="El numero de telefono debe tener 10 digitos." required>
					</div>
					<div class="flex flex-col gap-1 md:col-span-2">
						<label for="address">Domicilio </label>
						<input type="text" id="addressPerf" class="input-text" v-model="newUserInfo.domicilio" required>
					</div>
					<div class="flex flex-col gap-1">
						<label for="password">Contraseña</label>
						<input type="password" id="passwordPerf" class="input-text" v-model="newUserInfo.password">
					</div>
					<div v-if="newUserInfo.password" class="flex flex-col gap-1">
						<label for="password-confirmation">Repetir contraseña</label>
						<input type="password" id="password-confirmationPerf" class="input-text" v-model="newUserInfo.passwordConfirm" required>
					</div>
				</div>
				<button type="submit" class="btn border-green-700 text-green-700">Guardar</button>
			</div>
		</div>
	</form>
	<div class="container mx-auto flex gap-10">
		<div class="flex-grow flex flex-col gap-3">
			<div class="flex justify-between items-center gap-3 px-3">
				<p class="text-blue-800 text-3xl">Mi Perfil</p>
			</div>
			<div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-4 p-3">
				<div class="flex flex-col gap-1">
					<label>Nombres</label>
					<p>{{ userInfo.nombre }}</p>
				</div>
				<div class="flex flex-col gap-1">
					<label>Apellidos</label>
					<p>{{ userInfo.apellido }}</p>
				</div>
				<div class="flex flex-col gap-1">
					<label>Correo electrónico</label>
					<p>{{ userInfo.email }}</p>
				</div>
				<div class="flex flex-col gap-1">
					<label>Teléfono</label>
					<p>{{ userInfo.telefono }}</p>
				</div>
				<div class="flex flex-col gap-1">
					<label>Domicilio</label>
					<p>{{ userInfo.domicilio }}</p>
				</div>
				<div class="flex flex-col md:flex-row items-stretch md:items-center gap-4 md:col-span-2 xl:col-span-4">
					<button type="button" class="btn border-blue-700 text-blue-700" @click="showModal('edit-profile-modal')">Modificar datos personales</button>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import {showModal,closeModal,closeAllModals} from '@/helpers'
import { userInfo } from '@/router/index.js';
import { supabase } from '../../supabase.js';
import { ref } from 'vue';

const newUserInfo = ref([]); 
newUserInfo.value.nombre = userInfo.value.nombre;
newUserInfo.value.apellido = userInfo.value.apellido;
newUserInfo.value.email = userInfo.value.email;
newUserInfo.value.telefono = userInfo.value.telefono;
newUserInfo.value.domicilio = userInfo.value.domicilio;

async function modificarPerfil(){
	if(newUserInfo.value.password !== newUserInfo.value.passwordConfirm){
		alert('La contraseña no coincide con la confirmacion.');
		return;
	}
	let userData;
	if(newUserInfo.value.password){
		userData = {
			nombre: newUserInfo.value.nombre,
			apellido: newUserInfo.value.apellido,
			telefono: newUserInfo.value.telefono,
			domicilio: newUserInfo.value.domicilio,
			email: newUserInfo.value.email,
			contrasena: newUserInfo.value.password,
		};
	} else {
		userData = {
			nombre: newUserInfo.value.nombre,
			apellido: newUserInfo.value.apellido,
			telefono: newUserInfo.value.telefono,
			domicilio: newUserInfo.value.domicilio,
			email: newUserInfo.value.email,
		};
	}
	const { data, error: errorInsertar } = await supabase
		.from('empleados')
		.update(userData)
		.eq('id_empleados',userInfo.value.nro_legajo)
	if(errorInsertar){
		alert('Hubo un error al intentar modificar los datos, verifique que todos los campos sean validos.');
		console.error(errorInsertar);
	}
	else {
		alert('Datos modificados con exito.');
		actualizarDatosPersonales();
		closeModal('edit-profile-modal');
	}
}

async function actualizarDatosPersonales(){
	const { data: newUserData, error: updateDataError } = await supabase
		.from('empleados')
		.select('*')
		.eq('id_empleados', userInfo.value.nro_legajo);
	if(updateDataError){
		alert('Error al actualizar los datos personales.');
		console.error(updateDataError);
	} else {
		userInfo.value.nombre = newUserData[0].nombre;
		userInfo.value.apellido = newUserData[0].apellido;
		userInfo.value.telefono = newUserData[0].telefono;
		userInfo.value.domicilio = newUserData[0].domicilio;
		userInfo.value.email = newUserData[0].email;
	}

}
</script>