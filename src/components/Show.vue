<template>
    <div>
        <span>{{ count }}</span>
        <circle-process>
            
        </circle-process>
    </div>
</template>
<script lang="ts">
import { ref, defineComponent } from "vue";
import CircleProcess from '@component/CircleProcess.vue'
export default defineComponent({
    name: "Show",
    components: {
        CircleProcess
    },
    setup: () => {
        const count = ref(0);
        return { count };
    },
});
</script>