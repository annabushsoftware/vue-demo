<template>
    <div>
        <h1>Schedule Demo View</h1>
        <b-table fixed bordered table-variant="light" :fields="this.tableFields" :items="this.tableData">
            <template #cell()="data">
                <ScheduleItem :day="data.value.dayAbbr" :hour="data.value.hour" :min="data.value.min"
                    @click="handleCellClick(data.value.dayAbbr, data.item.rowKey)" />
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
            activeCell: null
        }
    },
    computed: {
        tableData() {
            return [
                this.getTableRow(0),
                this.getTableRow(1)
            ]
        }
    },
    // mounted() {
    //     console.log(this.tableData)
    // },
    methods: {
        getTableRow(rowIndex) {
            var rowObj = {};
            days.forEach(day => {
                rowObj[day] = {
                    dayAbbr: day,
                    hour: '12',
                    min: '00'
                }
            });
            rowObj.rowKey = rowIndex;
            
            if(this.activeCell) {
                var dayRow = this.activeCell.split('-');
                if(dayRow[1] === rowIndex.toString()) {
                    rowObj._cellVariants = { [dayRow[0]]: 'info' }
                }
            }
            return rowObj;
        },
        handleCellClick(dayAbbr, rowKey) {
            var newActiveCell = `${dayAbbr}-${rowKey}`;
            if(this.activeCell && this.activeCell === newActiveCell) {
                this.activeCell = null;
            } else {
                this.activeCell = newActiveCell;
            }
        }
    }

}
</script>
