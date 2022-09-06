<template>
	<div
		class="cabine"
		:style="{
			bottom: 100 * floorNumber + 'px',
			transition: Math.abs(countFloorsBetween) + 's bottom linear',
		}"
		:class="{ primary: currentFloor.waiting }"
	>
		<div class="screen">
			<span>{{ whereCabibeGoing }}</span>
		</div>
	</div>
</template>

<script>
export default {
	name: "cabin-item",
	props: {
		queue: Array,
		currentFloor: Object,
		countFloorsBetween: Number,
	},
	computed: {
		floorNumber() {
			// todo: посмотреть еще
			if (this.queue.length) return this.queue[0].item;
			if (this.currentFloor.item > 0) return this.currentFloor.item;
			return 0;
		},
		whereCabibeGoing() {
			if (!this.queue.length) return "";
			else {
				let result = this.queue[0].item + 1;
				if (this.countFloorsBetween > 0) return result + "↑";
				else return result + "↓";
			}
		},
	},
};
</script>
<style>
.cabine {
	height: 100px;
	width: 80px;
	background-color: rgb(50, 161, 180);
	position: absolute;
	bottom: 0;
	transition-delay: 0;
}

.cabine.primary {
	animation-name: blink;
	animation-timing-function: linear;
	animation-duration: 1s;
	animation-iteration-count: infinite;
}

.cabine .screen {
	width: 30px;
	margin: 5px auto;
	background-color: rgba(0, 0, 0, 0.623);
	border-radius: 3px;
	color: white;
}

@keyframes blink {
	50% {
		opacity: 0.3;
	}
}
</style>
