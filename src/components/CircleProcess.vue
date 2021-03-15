<template>
    <div>
        <span>{{ count }}</span>
    </div>
</template>
<script lang="ts">
import { ref, defineComponent } from "vue";
export default defineComponent({
    name: "CircleProcess",
    setup: () => {
        const count = ref(0);
        return { count };
    },
});
</script>