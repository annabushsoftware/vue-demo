<template>
    <div>
        <h1>Schedule Demo View</h1>
        <b-table fixed bordered table-variant="light" :items="this.tableData" :fields="this.tableFields">
            <template #cell()="data">
                <ScheduleItem :day="data.value.dayAbbr" :hour="data.value.hour" :min="data.value.min" />
            </template>
        </b-table>
    </div>
</template>

<script>
import ScheduleItem from '../components/ScheduleItem.vue'

const days = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'];

export default {
    components: {
        ScheduleItem
    },
    data() {
        return {
            tableFields: days,
            tableData: [
                this.getTableRow(),
                this.getTableRow()
            ]
            /*
            [ 
                {
                    sun: {},
                    mon: {},
                    tues: {}
                },
                ...
            ]
            */
        }
    },
    // computed: {
        
    // },
    // mounted() {
    //     console.log(this.tableData)
    // },
    methods: {
        getTableRow() {
            var rowObj = {};
            days.forEach(day => {
                rowObj[day] = {
                    dayAbbr: day,
                    hour: '12',
                    min: '00',
                    active: false
                }
            })
            return rowObj;
        },
    }

}
</script>
