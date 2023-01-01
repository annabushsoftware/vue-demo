<template>
    <div class="schedule-item">
        <h2>{{ this.hour }}:{{ this.min }}</h2>
    </div>
</template>

<script>
export default {
    props: {
        day: {
            type: String,
            required: true,
            default: 'Item'
        },
        hour: {
            type: String,
            required: true
        },
        min: {
            type: String,
            required: true
        }
    }
}
</script>

<style lang="scss" scoped>
.schedule-item {
    padding: 0.5em;
}
</style>
