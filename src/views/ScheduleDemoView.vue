<template>
    <div>
        <h1>Schedule Demo View</h1>
        <b-table fixed bordered table-variant="light" :fields="this.tableFields" :items="this.tableData">
            <template #cell()="data">
                <div :id="this.getCellRef(data.value.dayAbbr, data.item.rowKey)"
                     :ref="this.getCellRef(data.value.dayAbbr, data.item.rowKey)">
                    <ScheduleItem :day="data.value.dayAbbr" :hour="data.value.hour" :min="data.value.min"
                        @click="handleCellClick(data.value.dayAbbr, data.item.rowKey)" />
                </div>
                <b-popover :target="this.$refs[this.getCellRef(data.value.dayAbbr, data.item.rowKey)]" triggers="click"
                    placement="bottom" :ref="this.getPopoverRef(data.value.dayAbbr, data.item.rowKey)">
                    Popover content
                </b-popover>
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
            if(this.activeCell) {
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
                    hour: '12',
                    min: '00'
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
        getPopoverRef(dayAbbr, rowKey) {
            return `popover-${dayAbbr}-${rowKey}`;
        },
        handleCellClick(dayAbbr, rowKey) {
            var newActiveCell = `${dayAbbr}-${rowKey}`;
            
            if (this.activeCell && this.activeCell === newActiveCell) {
                this.activeCell = null;
            } else {
                if(this.activeCell) {
                    // close old popover
                    var ref = this.getPopoverRef(this.activeDayRow[0], this.activeDayRow[1]);
                    console.log('ref: ', this.$refs[ref])
                    // this.$refs[ref].$emit('hidden');
                }
                
                this.activeCell = newActiveCell;
                // this.placeholderProp = true;
                // this.$refs[].$emit('hide.bs.popover')

                // this.$refs[this.getPopoverRef(dayAbbr, rowKey)].$emit('show');
            }
        }
    }

}
</script>
