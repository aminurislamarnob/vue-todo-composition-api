<template>
    <div class="app-container">
        <Header>Vuejs todos</Header>
        <slot />
    </div>
</template>

<script setup>
import Header from './Header.vue';
</script>