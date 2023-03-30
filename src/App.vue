<template>
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
			:queue="queue"
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
			if (!(this.cabines.filter((c) => c.currentFloor === floor.item).length === 0)) return;

			if (this.queue.filter((f) => f.item === floor.item).length === 0) {
				this.queue.push(floor);
			}
			const cabineToGo = this.findFreeAndClosestCabine(floor.item);
	
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
			const cabinePositions = freeCabines.map(
				(item) => item.currentFloor
			);
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
		if (localStorage.getItem("floors")) {
			this.floors = JSON.parse(localStorage.getItem("floors"));
			for (let i = 0; i < this.floors.length; i++) {
				this.floors[i].waiting = false;
			}
		} else {
			for (let i = 0; i < floors; i++) {
				this.floors.push({ item: i, waiting: false });
			}
			this.floors.reverse();
		}

		if (localStorage.getItem("cabines")) {
			this.cabines = JSON.parse(localStorage.getItem("cabines"));
			for (let i = 0; i < this.cabines.length; i++) {
				this.cabines[i].inAction = false;
				this.cabines[i].open = false;
			}
		} else {
			for (let i = 0; i < cabines; i++) {
				this.cabines.push({
					id: i,
					currentFloor: 0,
					open: false,
					inAction: false,
					countFloorsBetween: null,
				});
			}
		}

		if (localStorage.getItem("queue")) {
			this.queue = JSON.parse(localStorage.getItem("queue"));
			if (this.queue.length > this.cabines.length) {
				this.queue = this.queue.slice(
					this.cabines.length,
					this.queue.length
				);
			} else this.queue = [];
		}
		if (this.queue.length > 0)
			setTimeout(() => {
				for(let i =0; i < this.queue.length; i++){
					this.callElevatorToFloor(this.queue[i]);
				}
			}, 1000);
	},
	watch: {
		queue: {
			handler() {
				localStorage.setItem("queue", JSON.stringify(this.queue));
			},
			deep: true,
		},
		cabines: {
			handler() {
				localStorage.setItem("cabines", JSON.stringify(this.cabines));
			},
			deep: true,
		},
		floors: {
			handler() {
				localStorage.setItem("floors", JSON.stringify(this.floors));
			},
			deep: true,
		},
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
