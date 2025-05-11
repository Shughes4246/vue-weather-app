<template>
    <div>
        <h2 class="text-xl font-bold mb-4 text-center">3-Day Forecast</h2>
        <div class="grid md:grid-cols-3 gap-6">
            <div
                v-for="day in forecast.forecast.forecastday"
                :key="day.date"
                class="bg-gray-100 p-4 rounded-lg text-center shadow-sm"
            >
                <p class="font-medium mb-1">{{ formatFullDate(day.date) }}</p>
                <img
                    class="h-12 w-12 mx-auto mb-1"
                    :src="day.day.condition.icon"
                    :alt="day.day.condition.text"
                />
                <p class="text-sm mb-1">{{ day.day.condition.text }}</p>
                <p class="text-sm">
                    {{ useFahrenheit ? day.day.maxtemp_f : day.day.maxtemp_c }}°
                    /
                    {{ useFahrenheit ? day.day.mintemp_f : day.day.mintemp_c }}°
                    {{ useFahrenheit ? "F" : "C" }}
                </p>
                <p class="text-xs">
                    🌧️ {{ day.day.daily_chance_of_rain }}% | 🌞 UV:
                    {{ day.day.uv }}
                </p>
                <p class="text-xs">
                    🌅 {{ day.astro.sunrise }} | 🌇 {{ day.astro.sunset }}
                </p>
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    forecast: Object,
    useFahrenheit: Boolean,
});

function formatFullDate(dateString) {
    const date = new Date(dateString);
    return date.toLocaleDateString(undefined, {
        weekday: "long",
        month: "long",
        day: "numeric",
    });
}
</script>
