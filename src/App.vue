<template>
  <div class="elevator">
    <cabin-item :queue="queue" :currentFloor="currentFloor" :countFloorsBetween="countFloorsBetween"></cabin-item>
    <floor-component 
    v-for="item in floors" 
    :key="item" 
    :floor="item"
    :currentFloor="currentFloor"
    v-model:queue="queue"
    @call="callElevatorToFloor"
    ></floor-component>
  </div>
    queue: {{queue}} <br>
    currentFloor: {{currentFloor}}
</template>

<script>
import FloorComponent from './components/FloorComponent.vue'
import CabinItem from './components/CabinItem.vue';

export default {
  name: 'App',
  components: {
    FloorComponent,
    CabinItem
},
data() {
  return {
    floors: [
      {
      item: 4,
      waiting: false
      },
      {
      item: 3,
      waiting: false
      },
      {
      item: 2,
      waiting: false
      },
      {
      item: 1,
      waiting: false
      },
      {
      item: 0,
      waiting: false
      }
  ],
  queue: [],
  currentFloor: {item: 0, waiting: false},
  countFloorsBetween: 0,
  isGoing: false
  }
},
methods: {
  callElevatorToFloor() {
      if(this.isGoing) return;
      this.isGoing = true;
      this.countFloorsBetween = Math.abs(this.queue[0].item - this.currentFloor.item)
      setTimeout(() => {
            this.setCurrentFloor()
      }, this.countFloorsBetween * 1000)
  },
  setCurrentFloor() {
    this.currentFloor = {...this.queue[0]}
    this.currentFloor.waiting = true;
    setTimeout(() => {
      this.currentFloor.waiting = false;
      this.queue.shift()
      this.isGoing = false;
      if(this.queue.length) this.callElevatorToFloor()
    }, 3000)
  }
}
}
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
