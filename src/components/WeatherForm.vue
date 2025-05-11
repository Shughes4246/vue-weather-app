<template>
    <form
        @submit.prevent="handleSubmit"
        class="flex items-center gap-4 w-full max-w-3xl mx-auto px-4 py-2"
    >
        <input
            v-model="city"
            type="text"
            placeholder="Enter a city"
            required
            class="flex-grow p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <button
            type="submit"
            class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition"
        >
            Get Weather
        </button>
    </form>
</template>

<script setup>
import { ref } from "vue";

const city = ref("");

const emit = defineEmits(["search"]);

function handleSubmit() {
    emit("search", city.value);
    city.value = "";
}
</script>
