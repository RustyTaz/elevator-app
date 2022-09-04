<template>
    <div class="floor">
        <div class="shahta"></div>
        <div class="floor-screen">
            <span>{{floor.item + 1}}</span>
            <button 
            class="floor-button" 
            :class="{'floor-button-active': isWaitingCabine}" 
            :disabled="currentFloor.item === floor.item || isWaitingCabine"
            @click="callElevator" 
            >нажать</button>
            {{isWaitingCabine}}
        </div>
    </div>
    
</template>

<script>
    export default {
        name: 'floor-component',
        props: {
            floor: Object,
            queue: Array,
            currentFloor: Object
        },
        methods: {
            callElevator() {
                this.$emit('update:queue', [...this.queue, this.floor])
                this.$emit('call')
            }
        },
        computed: {
            isWaitingCabine() {
                // todo: может быть стоит проверять что первый элемент равен floor.item
                return this.queue.some(floor => floor.item === this.floor.item)
            }
        }
    }
</script>

<style>
    .floor {
        height: 100px;
        width: 100%;
        border-top: 2px solid black;
        display: flex;
        gap: 10px;
    }
    .floor:last-child {
        border-bottom: 2px solid black;
    }
    .floor-screen {
        display: flex;
        flex-direction: column;
    }
    .floor-button-active {
        background-color: blue;
    }
    .shahta {
        width: 80px;
        height: 100%;
        border-left: 1px solid gray;
        border-right: 1px solid gray;
    }
</style>