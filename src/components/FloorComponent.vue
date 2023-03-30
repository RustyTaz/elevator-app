<template>
	<div
		class="floor"
		:style="{
			width: 90 * shaftCount + 20 + 'px',
		}"
	>
		<div class="floor-screen">
			<span class="floor-number">{{ floor.item + 1 }}</span>
			<button
				class="floor-button"
				:class="{ 'floor-button-active': isWaitingCabine }"
				:disabled="
					(currentFloor === floor.item) || isWaitingCabine
				"
				@click="callElevator"
			>
				⦿
			</button>
		</div>
	</div>
</template>

<script>
export default {
	name: "floor-component",
	props: {
		floor: Object,
		queue: Array,
		currentFloor: Object,
		shaftCount: Number,
	},
	methods: {
		callElevator() {
			this.$emit("call", this.floor);
		},
	},
	computed: {
		isWaitingCabine() {
			return this.queue.some((floor) => floor.item === this.floor.item);
		},
	},
};
</script>

<style>
.floor {
	height: 100px;
	width: 100%;
	border-top: 1px solid lightgray;
	display: flex;
	gap: 10px;
	justify-content: flex-end;
}
.floor:last-child {
	border-bottom: 1px solid lightgray;
}
.floor-screen {
	display: flex;
	flex-direction: column;
}

.floor-button {
	height: 20px;
	width: 20px;
	color: rgb(50, 161, 180);
	border: rgb(50, 161, 180) solid 1px;
	z-index: 1000;
}

.floor-button:hover {
	background-color: rgb(204, 231, 233);
}

.floor-button-active {
	color: rgb(243, 192, 53);
	border: rgb(243, 192, 53) solid 1px;
}
.floor-number {
	font-weight: 600;
	text-align: left;
}
</style>