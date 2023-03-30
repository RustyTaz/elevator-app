<template>
  <div class="shaft"
       :style="{
			left: 90 * (cabin.id) + 'px',
		}">
    <div
        class="cabine"
        :style="{
			bottom: 100 * cabin.currentFloor + 'px',
			transition: Math.abs(cabin.countFloorsBetween) + 's bottom linear',
		}"
        :class="{ primary: cabin.open }"
    >
      <div class="screen">
        <span>{{ whereCabinGoing }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "cabin-item",
  props: {
    currentFloor: Object,
    cabin: Object,
    countFloorsBetween: Number,
  },
  computed: {
    whereCabinGoing() {
      if(this.cabin.inAction) {
        let result = this.cabin.currentFloor + 1;
        if (this.cabin.countFloorsBetween !== 0) result = result + (this.cabin.countFloorsBetween > 0 ? "↑" : "↓");
        return result
      }
      return null;
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

.shaft {
  position: absolute;
  border-left: 1px solid grey;
  border-right: 1px solid grey;
  width: 80px;
  height: 100%;
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