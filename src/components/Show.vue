<template>
    <div>
       <div>
           <button @click="toProcess">调到指定进度</button>
           <input type="text" v-model="process">
       </div>
        <circle-process :total="total1" :id="canvasId1" :list="list">
            
        </circle-process>

         <circle-process :total="total2" :id="'canvas2'" :radius="80" :fontColor="'green'"> 
            
        </circle-process>
    </div>
</template>
<script lang="ts">
import { ref, reactive, defineComponent } from "vue";
import CircleProcess from '@component/CircleProcess.vue'
export default defineComponent({
    name: "Show",
    components: {
        CircleProcess
    },
    setup: () => {
        const canvasId1 = ref('canvas1');
        const total1 = ref<number>(0)
        const process = ref(0)
        const toProcess = ():void => {
            total1.value = Number(process.value)
        }
        const canvasId2 = ref('canvas2');
        const total2 = ref(99.9)
        const list = reactive([
            {
                id: 1,
                val: 1
            },
            {
                id: 2,
                val: 2
            }
        ])
        return { 
            canvasId1,
            total1,
            process,
            canvasId2,
            total2,
            toProcess,
            list
        };
    },
});
</script>