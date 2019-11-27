<template>
	<div id="app">
		<DragonLights/>
	</div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import DragonLights from "./components/DragonLights.vue";

@Component({
	components: {
		DragonLights,
	},
})
export default class App extends Vue {
}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>
