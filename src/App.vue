<template>
	<FormMember />
</template>

<script>
import FormMember from "./components/FormMember.vue";

export default {
	name: "App",
	components: {
		FormMember,
	},
};
</script>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
body {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	text-align: center;
	color: #2c3e50;
}
</style>
