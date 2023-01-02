<template>
    <div>
        <h1>Schedule Demo View</h1>
        <b-table fixed bordered table-variant="light" :fields="this.tableFields" :items="this.tableData"
            table-class="schedule-table">
            <template #cell()="data">
                <div class="cell-container" :ref="this.getCellRef(data.value.dayAbbr, data.item.rowKey)">
                    <ScheduleItem :day="data.value.dayAbbr" :hour="data.value.hour" :min="data.value.min"
                        @click="onCellClick(data.value.dayAbbr, data.item.rowKey)" />
                </div>
                <b-popover :target="this.$refs[this.getCellRef(data.value.dayAbbr, data.item.rowKey)]"
                    placement="bottom">
                    <PopoverContent :hour="this.popoverContentProp(data.value.hour)" 
                        :min="this.popoverContentProp(data.value.min)"
                        @submitted="onPopoverSubmit" />
                </b-popover>
            </template>
        </b-table>
    </div>
</template>

<script>
import ScheduleItem from '../components/ScheduleItem.vue'
import PopoverContent from '../components/PopoverContent.vue';

const days = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'];
const timeSlots = ['firstTime', 'secondTime'];

export default {
    components: {
        ScheduleItem,
        PopoverContent
    },
    data() {
        return {
            tableFields: days,
            activeCell: null,
            dayTimeData: days.reduce((accumulator, day) => {
                accumulator[day] = {
                    [timeSlots[0]]: {
                        hour: '--',
                        min: '--'
                    },
                    [timeSlots[1]]: {
                        hour: '--',
                        min: '--'
                    }
                }
                return accumulator;
            }, {})
        }
    },
    computed: {
        tableData() {
            return [
                this.getTableRow(0),
                this.getTableRow(1)
            ]
        },
        activeDayRow() {
            if (this.activeCell) {
                return this.activeCell.split('-');
            } else {
                return null;
            }
        }
    },
    methods: {
        getTableRow(rowIndex) {            
            var rowObj = {
                rowKey: rowIndex
            };

            days.reduce((accumulator, day) => {
                accumulator[day] = {
                    dayAbbr: day,
                    hour: this.dayTimeData[day][timeSlots[rowIndex]].hour,
                    min: this.dayTimeData[day][timeSlots[rowIndex]].min
                };
                return accumulator;
            }, rowObj);

            if (this.activeCell && this.activeDayRow[1] === rowIndex.toString()) {
                rowObj._cellVariants = { [this.activeDayRow[0]]: 'info' }
            }

            return rowObj;
        },
        getCellRef(dayAbbr, rowKey) {
            return `schedule-item-${dayAbbr}-${rowKey}`;
        },
        popoverContentProp(hourMin) {
            if(hourMin === '--') {
                return undefined;
            } else {
                return hourMin;
            }
        },
        onCellClick(dayAbbr, rowKey) {
            var newActiveCell = `${dayAbbr}-${rowKey}`;

            if (this.activeCell && this.activeCell === newActiveCell) {
                this.activeCell = null;
            } else {
                this.closeActivePopover();
                this.activeCell = newActiveCell;
            }
        },
        onPopoverSubmit(hour, min) {
            console.log('hour: ', hour)
            console.log('min:', min)


        },
        closeActivePopover() {
            if (this.activeCell) {
                var ref = this.getCellRef(this.activeDayRow[0], this.activeDayRow[1]);
                this.$refs[ref].click();
            }
        }
    }

}
</script>

<style lang="scss">
.schedule-table td {
    padding: 0;

    .cell-container {
        cursor: pointer;
    }
}
</style>
