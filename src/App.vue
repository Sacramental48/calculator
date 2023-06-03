<template>
    <div class="wrapper">
        <MainLayout></MainLayout>
    </div>
</template>

<script setup>
import MainLayout from "./components/MainLayout.vue";
</script>

<style scoped lang="scss">
.wrapper {
    min-height: 100%;
    display: flex;
    flex-direction: column;
}
</style>