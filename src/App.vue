<template>
	{{ this.queue }}
	<div class="elevator">
		<cabin-item
			v-for="cabin in cabines"
			:key="cabin.id"
			:cabin="cabin"
			:queue="queue"
		></cabin-item>
		<floor-component
			v-for="item in floors"
			:key="item.item"
			:floor="item"
			:currentFloor="item.currentFloor"
			:shaftCount="cabines.length"
			v-model:queue="queue"
			@call="callElevatorToFloor"
		></floor-component>
	</div>
</template>

<script>
import FloorComponent from "./components/FloorComponent.vue";
import CabinItem from "./components/CabinItem.vue";
import { cabines, floors } from "./mock/mock";

export default {
	name: "App",
	components: {
		FloorComponent,
		CabinItem,
	},
	data() {
		return {
			floors: [],
			cabines: [],
			queue: [],
		};
	},
	methods: {
		callElevatorToFloor(floor) {
			if (
				!(
					this.cabines.filter((c) => c.currentFloor === floor.item)
						.length === 0
				)
			)
				return;
			if (this.queue.filter((f) => f.item === floor.item).length === 0) {
				this.queue.push(floor);
			}
			const cabineToGo = this.findFreeAndClosestCabine(floor.item);
			console.log(cabineToGo);
			if (!cabineToGo) return;
			floor.waiting = true;
			cabineToGo.countFloorsBetween =
				floor.item - cabineToGo.currentFloor;
			cabineToGo.currentFloor = floor.item;
			cabineToGo.inAction = true;
			setTimeout(() => {
				this.setCurrentFloor(cabineToGo, floor);
			}, Math.abs(cabineToGo.countFloorsBetween) * 1000);
		},

		findFreeAndClosestCabine(desiredFloor) {
			const freeCabines = this.cabines.filter((c) => !c.inAction);
			console.log(freeCabines);
			const cabinePositions = freeCabines.map(
				(item) => item.currentFloor
			);
			console.log(cabinePositions);
			if (!cabinePositions) return;
			const cabineToGoPosition = cabinePositions.reduce((prev, curr) =>
				Math.abs(curr - desiredFloor) < Math.abs(prev - desiredFloor)
					? curr
					: prev
			);
			return this.cabines.find(
				(item) => item.currentFloor === cabineToGoPosition
			);
		},

		setCurrentFloor(cabin, floor) {
			cabin.open = true;
			setTimeout(() => {
				cabin.open = false;
				cabin.inAction = false;
				floor.waiting = false;
				this.queue.shift();
				if (this.queue.filter((f) => !f.waiting).length)
					this.callElevatorToFloor(
						this.queue.find((f) => !f.waiting)
					);
			}, 3000);
		},
	},
	mounted() {
		for (let i = 0; i < floors; i++) {
			this.floors.push({ item: i, waiting: false });
		}
		this.floors.reverse();
		for (let i = 0; i < cabines; i++) {
			this.cabines.push({
				id: i,
				currentFloor: 0,
				open: false,
				inAction: false,
				countFloorsBetween: null,
			});
		}
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
	width: 1000px;
	position: relative;
	margin: 20px;
}
</style>
