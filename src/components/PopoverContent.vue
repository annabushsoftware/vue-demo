<template>
    <b-form>
        <b-form-group label="Select Hour" label-for="hour-input" description="(24 hour clock format)">
            <b-form-input id="hour-input" type="text" required  />
        </b-form-group>
    </b-form>
    <label class="text-dark">{{ this.hour }}:{{ this.min }}</label>
</template>

<script>
export default {
    props: {
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
    },
    data() {
        return {
            hourInput: '12',
            minInput: '00'
        }
    }
}
</script>
