<template>
    <div>
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
            required: false,
            default: '12'
        },
        min: {
            type: String,
            required: false,
            default: '00'
        }
    }
}
</script>
