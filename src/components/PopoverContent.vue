<template>
    <b-form @submit="onSubmit">
        <b-form-group label="Select Time" label-for="time-input">
            <b-form-input id="time-input" type="time" v-model="timeInput" required  />
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
    </b-form>
</template>

<script>
export default {
    emits: {
        submitted(hour, min) {
            if(hour && hour !== '--' &&  min && min !== '--') {
                return true;
            } else {
                console.log('invalid input')
                return false;
            }
        }
    },
    props: {
        hour: {
            type: String,
            required: false,
            default: '12' // TODO: change to -- so form starts blank
        },
        min: {
            type: String,
            required: false,
            default: '00'
        }
    },
    data() {
        return {
            timeInput: '' // 24  hour format, HH:mm
        }
    },
    mounted() {
        this.timeInput = `${this.hour}:${this.min}`;
    },
    methods: {
        onSubmit() {
            var hourMin = this.timeInput.split(':');
            this.$emit('submitted', hourMin[0], hourMin[1]);
        }
    }
}
</script>
