<script setup>
import TheInventory from './components/TheInventory.vue'
</script>

<template>
	<TheInventory />
</template>

<style>
*,
*::before,
*::after {
	box-sizing: border-box;
}
body {
	margin: 0;
}
ul {
	padding: 0;
	margin: 0;
	list-style-type: none;
}
input,
button {
	outline: none;
}
#app {
	height: 100vh;
	display: flex;
	align-items: center;
	justify-content: center;
	font-family: 'Inter';
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}

.inventory__skeleton {
	background: linear-gradient(90deg, #3c3c3c 0%, #444444 51.04%, #333333 100%);
}
</style>
