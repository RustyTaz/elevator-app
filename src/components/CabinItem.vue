<template>
    <div class="cabine" :style="{bottom: 100 * floorNumber + 'px', transition: countFloorsBetween + 's bottom linear'}" :class="{primary: currentFloor.waiting}">
        {{queue.length ? queue[0].item + 1: ''}}
    </div>
    countFloorsBetween {{countFloorsBetween}} <br>
    floorNumber {{floorNumber}}
</template>

<script>
    export default {
        name: 'cabin-item',
        props: {
            queue: Array,
            currentFloor: Object,
            countFloorsBetween: Number
        },
        computed: {
            floorNumber() {
                // todo: посмотреть еще 
                if(this.queue.length) return this.queue[0].item
                if(this.currentFloor.item > 0) return this.currentFloor.item
                return 0
            }
        }
    }
</script>
<style>
    .cabine {
        height: 100px;
        width: 80px;
        background-color: aquamarine;
        position: absolute;
        bottom: 0;
        transition-delay: 0;
    }

    .cabine.primary {
        background: red;
    }
</style>