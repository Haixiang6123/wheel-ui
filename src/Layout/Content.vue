<template>
    <div class="w-content">
        <slot></slot>
    </div>
</template>

<script>
    export default {
        name: "w-content"
    }
</script>

<style scoped>
    .w-content {
        flex-grow: 1;
    }
</style>