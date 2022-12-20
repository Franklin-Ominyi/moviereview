<template>
	<div>
		<MainComponent :imdbID="imdbID" />
		<FooterComponent />
	</div>
</template>

<script>
import MainComponent from "./components/main/Main.vue";
import FooterComponent from "../footer/Footer.vue";

export default {
	components: {
		MainComponent,
		FooterComponent,
	},
	props: ["imdbID"],
};
</script>

<style scoped>
div {
	position: relative;
	padding-top: 130px;
	min-height: 100vh;
}
</style>
