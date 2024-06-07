<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <p class="text-white text-2xl text-center py-4">Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
</script>