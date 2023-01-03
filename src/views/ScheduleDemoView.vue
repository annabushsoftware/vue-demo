<template>
    <div>
        <b-table fixed bordered table-variant="light" table-class="schedule-table"
                 :fields="this.tableFields"
                 :items="this.tableData">
            <template #cell()="data">
                <!-- TODO: make cells wider and center text so adding times will look good -->
                <div class="cell-container" :ref="this.getCellRef(data.value.dayAbbr, data.item.rowKey)">
                    <ScheduleItem :day="data.value.dayAbbr"
                                  :hour="data.value.hour"
                                  :min="data.value.min"
                                  @click="onCellClick(data.value.dayAbbr, data.item.rowKey)" />
                </div>
                <b-popover :target="this.$refs[this.getCellRef(data.value.dayAbbr, data.item.rowKey)]"
                           placement="bottom">
                    <PopoverContent :hour="this.getTimeOrUndefined(data.value.hour)"
                                    :min="this.getTimeOrUndefined(data.value.min)"
                                    @submitted="onPopoverSubmit" />
                </b-popover>
            </template>
        </b-table>
    </div>
</template>

<script>
import ScheduleItem from '../components/ScheduleItem.vue'
import PopoverContent from '../components/PopoverContent.vue';
import moment from 'moment';

const days = ['Sun', 'Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat'];
const timeSlots = ['firstTime', 'secondTime'];

class SlotOrderingClassifiers {
    static valid = Symbol('valid');
    static same = Symbol('same');
    static reversed = Symbol('reversed');
}

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
        getTimeOrUndefined(hourMin) {
            if (!hourMin || hourMin.length < 2 || hourMin === '--') {
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
            var day = this.activeDayRow[0];
            var timeSlot = timeSlots[parseInt(this.activeDayRow[1])];
            this.sortAndApplyTimes(day, timeSlot, hour, min);

            this.closeActivePopover();
            this.activeCell = null;
        },
        sortAndApplyTimes(submittedDay, submittedTimeSlot, submittedHour, submittedMin) {
            var submittedSlotIsFirst = submittedTimeSlot === timeSlots[0];

            var theOtherSlot = submittedSlotIsFirst ? timeSlots[1] : timeSlots[0];
            var theOtherHour = this.dayTimeData[submittedDay][theOtherSlot].hour;
            var theOtherMin = this.dayTimeData[submittedDay][theOtherSlot].min;

            var slotOrderingClassifier = this.validateSlotOrdering(submittedSlotIsFirst, submittedHour, submittedMin, theOtherHour, theOtherMin);
            switch (slotOrderingClassifier) {
                case SlotOrderingClassifiers.equal:
                    this.dayTimeData[submittedDay] = {
                        [submittedTimeSlot]: {
                            hour: submittedHour,
                            min: submittedMin
                        },
                        [theOtherSlot]: {
                            hour: '--',
                            min: '--'
                        }
                    }
                    break;
                case SlotOrderingClassifiers.reversed:
                    this.dayTimeData[submittedDay] = {
                        [submittedTimeSlot]: {
                            hour: theOtherHour,
                            min: theOtherMin
                        },
                        [theOtherSlot]: {
                            hour: submittedHour,
                            min: submittedMin
                        }
                    }
                    break;
                case SlotOrderingClassifiers.valid:
                default:
                    this.dayTimeData[submittedDay][submittedTimeSlot] = {
                        hour: submittedHour,
                        min: submittedMin
                    }
                    break;
            }
        },
        validateSlotOrdering(submittedSlotIsFirst, submittedHour, submittedMin, theOtherHour, theOtherMin) {
            var otherSlotPopulated = this.getTimeOrUndefined(theOtherHour) && this.getTimeOrUndefined(theOtherMin);
            if (otherSlotPopulated) {
                var submittedTime = this.convertTimeToMoment(submittedHour, submittedMin);
                var theOtherTime = this.convertTimeToMoment(theOtherHour, theOtherMin);

                if (submittedTime.isSame(theOtherTime)) {
                    return SlotOrderingClassifiers.equal;
                }
                else if (submittedSlotIsFirst && submittedTime.isBefore(theOtherTime)) {
                    return SlotOrderingClassifiers.valid;
                }
                else if (!submittedSlotIsFirst && theOtherTime.isBefore(submittedTime)) {
                    return SlotOrderingClassifiers.valid;
                } else {
                    return SlotOrderingClassifiers.reversed;
                }

            } else {
                return SlotOrderingClassifiers.valid;
            }
        },
        convertTimeToMoment(hour, min) {
            return moment([2000, 1, 1, parseInt(hour), parseInt(min), 0, 0]);
        },
        closeActivePopover() {
            /*
            Note: this.activeCell MUST stay tightly coupled with the popover visibility.
                This means that if you call this method to close a popover, 
                this.activeCell should be updated accordingly, either to a new value or reset to null.
            TODO Possible refactor: provide new value for this.activeCell as an argument and set it in this method
            */
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
