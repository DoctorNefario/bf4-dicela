<template>
	<div class="dragon-lights">
		<div class="light-switches" ref="switch-check">
			<button @click="onStartClick()">Start</button>
			<br>
			<span :key="i" v-for="(name, i) in switchNames">
				<label><input :value="i" type="checkbox" v-model="switchCheck">{{ name }}</label><br>
			</span>
		</div>
		<div class="dragon-buttons">
			<button @click="attemptSolve">Solve</button>
			<br>
			<button @blur="onResetBlur" @click="onResetClick" @mouseleave="onResetLeave">Reset ({{reset}})</button>
			<br>
		</div>
		<div class="light-table-wrapper">
			<table>
				<tr :key="y" v-for="y in lightY">
					<td :key="x" v-for="x in y === 1 || y === lightY ? lightX : 2">
						<a @click="onLightClick" @mouseenter="switchCheck.length < 2 ? $event.target.focus() : false"
						   @mouseleave="$event.target.blur()" href="#" ref="dragonLight"/>
					</td>
				</tr>
			</table>
		</div>
		<DragonInstructions class="instructions"/>
	</div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import DragonInstructions from "./DragonInstructions.vue";

@Component({
	components: { DragonInstructions },
})
export default class DragonLights extends Vue {
	lightX = 7;
	lightY = 5;

	lightCount = (this.lightX + this.lightY - 2) * 2;

	startState = 0;

	reset = 3;

	switchCheck: number[] = [];
	switchNames = [
		"South of H",
		"Southwest of E",
		"North of waterfall",
		"South of G",
		"East of C",
		"On top of B",
		"Tree between A and B",
	];

	switchStates: number[] = new Array(this.switchNames.length).fill(0);

	@Watch("switchCheck")
	onCheckboxChange() {
		this.drawCurrent();
	}

	attemptSolve() {
		const correctState = (1 << this.lightCount) - 1;
		const startState = this.startState;

		let answer = -1;
		for (let i = 0; i < 128; ++i) {
			let attempt = startState;
			for (let e = 0; e < 7; ++e) {
				const bit = i & (1 << e);
				if (bit) attempt ^= this.switchStates[e];
			}
			if (attempt === correctState) {
				answer = i;
				break;
			}
		}

		this.switchCheck = [];
		if (answer === -1) return;

		for (let e = 0; e < 7; ++e) {
			const bit = answer & (1 << e);
			if (bit) this.switchCheck.push(e);
		}
	}

	onLightClick(event: MouseEvent) {
		event.preventDefault();
		(event.target as HTMLElement).blur();
		if (this.switchCheck.length > 1) return;

		const lightNumber = (this.$refs.dragonLight as HTMLElement[]).findIndex(el => el === event.target);
		const bit = 1 << lightNumber;

		if (this.switchCheck.length === 1)
			this.switchStates[this.switchCheck[0]] ^= bit;
		else this.startState ^= bit;

		this.drawCurrent();
	}

	onStartClick() {
		this.switchCheck = [];
	}

	onResetClick(event: MouseEvent) {
		if (--this.reset > 0) return;
		this.reset = 3;

		this.startState = 0;
		this.switchStates.fill(0);
		this.onStartClick();
		const t = event.target as HTMLButtonElement;
		t.disabled = true;
	}

	onResetLeave(event: MouseEvent) {
		const t = event.target as HTMLButtonElement;
		t.blur();
		t.disabled = false;
	}

	onResetBlur(event: MouseEvent) {
		this.reset = 3;
		(event.target as HTMLButtonElement).disabled = false;
	}

	drawCurrent() {
		let toDraw = this.startState;

		for (let i of this.switchCheck) toDraw ^= this.switchStates[i];

		for (let i = 0; i < this.lightCount; ++i) {
			const cl = (this.$refs.dragonLight as HTMLElement[])[i].classList;

			const mask = 1 << i;
			const state = !!(toDraw & mask);
			const isStart = !!(this.startState & mask);

			cl.toggle("starting", state && isStart);
			cl.toggle("active", state && !isStart);
		}
	}

	mounted() {
		const td = document.createElement("td");
		td.colSpan = this.lightX - 2;
		td.rowSpan = this.lightY - 2;
		(this.$refs.dragonLight as HTMLElement[])[this.lightX].parentElement!.insertAdjacentElement("afterend", td);
	}
}
</script>

<style scoped>
.dragon-lights {
	display: flex;
	flex-flow: wrap;
	justify-content: space-around;
	align-items: center;
}

.instructions {
	min-width: 50%;
}

td a {
	display: block;
	width: 8em;
	height: 8em;
	background: black;
}

td a.starting {
	background: darkcyan;
}

td a.active {
	background: darkorange;
}

td a:focus {
	background: slateblue;
}

button {
	width: 100%;
}
</style>