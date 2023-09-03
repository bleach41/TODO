<script setup>
    import { ref, defineEmits } from "vue"
    const text = ref('')
    const emits = defineEmits(['onNewItem'])

    function handleSubmit() {
        if (text !== '') {
            //mandar a llamar el emit
            emits('onNewItem', text)
            text.value = ''

        }
    }

</script>

<template>
    <form action="" @submit.prevent="handleSubmit">
        <input type="text" v-model="text">
    </form>
</template>

<style scoped>
    input {
        height: 2em;
        background-color: rgba(57, 59, 61, 0.377);
        margin-bottom: 5px;
    }
</style>