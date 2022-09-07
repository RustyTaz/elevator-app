<template>
	<div class="elevator">
		<cabin-item
			:queue="queue"
			:currentFloor="currentFloor"
			:countFloorsBetween="countFloorsBetween"
		></cabin-item>
		<floor-component
			v-for="item in floors"
			:key="item"
			:floor="item"
			:currentFloor="currentFloor"
			:isGoing="isGoing"
			v-model:queue="queue"
			@call="callElevatorToFloor"
		></floor-component>
	</div>
</template>

<script>
import FloorComponent from "./components/FloorComponent.vue";
import CabinItem from "./components/CabinItem.vue";
import { floors } from "./mock/mock";

export default {
	name: "App",
	components: {
		FloorComponent,
		CabinItem,
	},
	data() {
		return {
			floors: [],
			queue: [],
			currentFloor: { item: 0, waiting: false },
			countFloorsBetween: 0,
			isGoing: false,
		};
	},
	methods: {
		callElevatorToFloor() {
			if (this.isGoing) return;
			this.isGoing = true;
			this.countFloorsBetween =
				this.queue[0].item - this.currentFloor.item;
			setTimeout(() => {
				this.setCurrentFloor();
			}, Math.abs(this.countFloorsBetween) * 1000);
		},
		setCurrentFloor() {
			this.currentFloor = this.queue[0];
			this.currentFloor.waiting = true;
			setTimeout(() => {
				this.currentFloor.waiting = false;
				this.queue.shift();
				this.isGoing = false;
				if (this.queue.length) this.callElevatorToFloor();
			}, 3000);
		},
	},
	mounted() {
		for (let i = 0; i < floors; i++) {
			this.floors.push({ item: i, waiting: false });
		}
		this.floors.reverse();
	},
};
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}

* {
	padding: 0;
	margin: 0;
	box-sizing: border-box;
}
.elevator {
	width: 600px;
	position: relative;
	margin: 20px;
}
</style>
