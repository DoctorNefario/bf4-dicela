<template>
	<div class="instructions">
		<ol>
			<li>Mark all the lights that are currently on</li>
			<li>Turn on a switch in-game, then select the corresponding switch here and mark down the new position of
				the lights
			</li>
			<li>Turn off the switch in-game</li>
			<li>Repeat steps 2-3 for every switch</li>
			<li>Once every switch has been toggled, new lights will be on for the start state. Mark down these new
				lights
			</li>
			<li>Click "Solve". Assuming you marked everything correctly, it will show the correct combination of
				switches you need to enable
			</li>
		</ol>
		<div>
			<p>
				You can only change the light states when 1 switch is enabled. If none are enabled, it will change the
				state of the starting lights.
			</p>
			<p>
				Pressing the reset button 3 times will reset everything back to the default state. Pressing the start
				button will turn off all the switches
			</p>
		</div>
	</div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component
export default class DragonInstructions extends Vue {

}
</script>

<style scoped>
.instructions {
	display: flex;
	justify-content: space-between;
}

.instructions * {
	margin: 0.5em 2%;
}
</style>