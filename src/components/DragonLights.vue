<template>
	<div class="dragon-lights">
		<div class="light-switches" ref="switch-check">
			<button @click="onStartClick()">Start</button>
			<br>
			<span :key="i" v-for="(name, i) in switchNames">
				<input :id="`check${name}`" :value="i" type="checkbox" v-model="switchCheck">
				<label :for="`check${name}`">{{ name }}</label><br>
			</span>
		</div>
		<div class="dragon-buttons">
			<button @click="attemptSolve">Solve</button>
			<br>
			<button @click="onResetClick" @mouseleave="reset = 3">Reset ({{reset}})</button>
			<br>
		</div>
		<div class="light-table-wrapper">
			<table>
				<tr v-for="y in lightY" :key="y">
					<td v-for="x in y === 1 || y === lightY ? lightX : 2" :key="x">
						<a ref="dragonLight" href="#"
						   @click="onLightClick"
						   @mouseenter="switchCheck.length < 2 ? $event.target.focus() : false"
						   @mouseleave="$event.target.blur()"></a>
					</td>
				</tr>
			</table>
		</div>
	</div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";

@Component
export default class DragonLights extends Vue {
	lightX = 7;
	lightY = 5;

	lightCount = (this.lightX + this.lightY - 2) * 2;

	startState = 0;

	reset = 3;

	switchCheck: number[] = [];
	switchNames = [
		"First",
		"Second",
		"Third",
		"Fourth",
		"Fifth",
		"Sixth",
		"Seventh",
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

	onResetClick() {
		if (--this.reset > 0) return;
		this.reset = 3;

		this.startState = 0;
		this.switchStates.fill(0);
		this.onStartClick();
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