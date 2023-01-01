<template>
    <div>
        <h1>Schedule Demo View</h1>
        <b-table fixed bordered table-variant="light" :fields="this.tableFields" :items="this.tableData"
            table-class="schedule-table">
            <template #cell()="data">
                <div class="cell-container" :ref="this.getCellRef(data.value.dayAbbr, data.item.rowKey)">
                    <ScheduleItem :day="data.value.dayAbbr" :hour="data.value.hour" :min="data.value.min"
                        @click="handleCellClick(data.value.dayAbbr, data.item.rowKey)" />
                </div>
                <b-popover :target="this.$refs[this.getCellRef(data.value.dayAbbr, data.item.rowKey)]"
                    placement="bottom">
                    <PopoverContent :hour="this.popoverContentProp(data.value.hour)" 
                        :min="this.popoverContentProp(data.value.min)" />
                </b-popover>
            </template>
        </b-table>
    </div>
</template>

<script>
import ScheduleItem from '../components/ScheduleItem.vue'
import PopoverContent from '../components/PopoverContent.vue';

const days = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'];

export default {
    components: {
        ScheduleItem,
        PopoverContent
    },
    data() {
        return {
            tableFields: days,
            activeCell: null,
            placeholderProp: false
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
    // mounted() {
    //     console.log(this.tableData)
    // },
    methods: {
        getTableRow(rowIndex) {
            var rowObj = {
                rowKey: rowIndex
            };

            days.forEach(day => {
                rowObj[day] = {
                    dayAbbr: day,
                    hour: '--',
                    min: '--'
                }
            });

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
        handleCellClick(dayAbbr, rowKey) {
            var newActiveCell = `${dayAbbr}-${rowKey}`;

            if (this.activeCell && this.activeCell === newActiveCell) {
                this.activeCell = null;
            } else {
                if (this.activeCell) {
                    // close old popover
                    var ref = this.getCellRef(this.activeDayRow[0], this.activeDayRow[1]);
                    this.$refs[ref].click();
                }

                this.activeCell = newActiveCell;
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
