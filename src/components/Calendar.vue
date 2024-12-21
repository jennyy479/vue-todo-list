<template>
    <div class="app-container">
        <div class="calendar-section">
                <q-btn size="sm" outline round color="primary" icon="add" @click="checkbox"/>
            <div class="calendar-header">
                <button @click="previousMonth">←</button>
                <h3>{{ nowMonth }} {{ currentYear }} 

                    
                </h3>
             
                <button @click="nextMonth">→</button>
            </div>
            <div class="calendar-grid" id="calendarGrid">
                <div class="day">日</div>
                <div class="day">一</div>
                <div class="day">二</div>
                <div class="day">三</div>
                <div class="day">四</div>
                <div class="day">五</div>
                <div class="day">六</div>
                <div class="day" v-for="day in daysOfMonth" :key="day.date" >
                    {{ day.day }}
                </div>
            </div>
        </div>


    </div>

</template>

<script setup lang="ts">
import { onMounted, ref, computed } from 'vue';
import dayjs from 'dayjs';


const currentDate = new Date();
const currentMonth = ref(+currentDate.getMonth());
const currentYear = ref(+currentDate.getFullYear());
const nowMonth = computed(() => {
    return monthNames[currentMonth.value] ?? undefined;
});
const monthIndex = ref();

const daysOfMonth = computed(() => getDaysOfMonth(currentYear.value, currentMonth.value));

function getDaysOfMonth(year: number, month: number) {
    const days = [];

    const firstDayOfMonth  = new Date(year, month, 1);
    const lastDayOfMonth = new Date(year, month + 1, 0);
    // 當月第一天是星期幾
    const dayOfWeek = firstDayOfMonth.getDay();
   
    // 是否第一筆數據正好是當前月份的第一天
	const isFirstDaySunday = dayOfWeek === 0;
    // 填充上個月的日期
    for (let i = dayOfWeek; i > 0; i--) {
        const day = new Date(year, month, -i + 1);
        days.push({
			date: dayjs(day).format('YYYYMMDD'),
			day: day.getDate(),
			month: day.getMonth(),
		});

       
    }

    // 填充當月的日期
    const numberOfDaysInMonth = lastDayOfMonth.getDate();
     console.log('lastDayOfMonth', lastDayOfMonth)
      console.log('numberOfDaysInMonth', numberOfDaysInMonth)
    for (let i = 1; i <= numberOfDaysInMonth; i++) {
       const date = new Date(year, month, i);
		days.push({
			date: dayjs(date).format('YYYYMMDD'),
			day: i,
			month: date.getMonth(),
		});
    }
    
    // 填充下個月的日期
    let daysInNextMonth = 1;
    while (days.length < 6 * 7) {
		const day = new Date(year, month + 1, daysInNextMonth);
		days.push({
			date: dayjs(day).format('YYYYMMDD'),
			day: day.getDate(),
			month: day.getMonth(),
		});
		daysInNextMonth++;
	}

    //如果不是從星期天開始，或者總天數超過35天，則填滿第六行
	if (!isFirstDaySunday || days.length > 35) {
		while (days.length < 6 * 7) {
			const day = new Date(year, month + 1, daysInNextMonth);
			days.push({
				date: dayjs(day).format('YYYYMMDD'),
				day: day.getDate(),
				selectable: false,
			});
			daysInNextMonth++;
		}
	}
    return days;
}

const monthNames = [
    "Jan", "Feb", "Mar", "Apr", "May", "Jun",
    "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
];


const isToday = (date: number) => {
    const today = new Date();
    const formattedToday = today.toISOString().split('T')[0];
    const formattedDate = formattedToday?.split('-')[2]
    return date.toString() === formattedDate;
};

const nextMonth = () => {
	if (currentMonth.value === 11) {
		currentMonth.value = 0;
		currentYear.value += 1;
	} else {
		currentMonth.value += 1;
	}

	// // 檢查下一個月份
	// if (isWithinThreeMonthsLater(`${currentYear.value}-${currentMonth.value + 1}-01`)) {
	// 	disabledNavigateNextButton.value = false;
	// 	disabledNavigatePrevButton.value = false;
	// } else {
	// 	disabledNavigateNextButton.value = true;
	// }
};

const previousMonth = () => {
    if (currentMonth.value === 0) {
		currentMonth.value = 11;
		currentYear.value -= 1;
	} else {
		currentMonth.value -= 1;
	}

};

onMounted(() => {
    monthIndex.value = new Date().getMonth();
    getDaysOfMonth(currentYear.value, monthIndex.value);
})
</script>
<style scoped>
.app-container {
    display: flex;
    width: 100%;
    height: 100vh;
}

.calendar-section {
    flex-grow: 2;
    background-color: white;
    padding: 20px;
    border-right: 1px solid #e0e0e0;
}

.schedule-section {
    flex-grow: 1;
    background-color: #f9f9f9;
    padding: 20px;
    overflow-y: auto;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header button {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    color: #333;
}

.current-month {
    font-size: 24px;
    font-weight: bold;
}

.calendar-grid {
   display: flex;
    flex-wrap: wrap;
}

.calendar-grid>div {
    aspect-ratio: 1 / 1;
    /* 保持正方形 */
    display: flex;
    justify-content: center;
}

.calendar-grid>div:first-child,
.calendar-grid>div:last-child {
    color: #888;
}

.calendar-day {
    padding: 15px;
    background-color: #f8f9fa;
    border-radius: 8px;
    position: relative;
    cursor: pointer;
    transition: all 0.3s ease;
}

.calendar-day:hover {
    background-color: #e9ecef;
}

.calendar-day.current-month {
    background-color: white;
    border: 1px solid #dee2e6;
}

.calendar-day.other-month {
    color: #aaa;
    background-color: #f1f3f5;
}

.calendar-day.has-event {
    background-color: #d1e7ff;
    font-weight: bold;
}

.event-indicator {
    position: absolute;
    top: 5px;
    right: 5px;
    width: 10px;
    height: 10px;
    background-color: #007bff;
    border-radius: 50%;
}

.schedule-form {
    display: flex;
    flex-direction: column;
    gap: 15px;
}

.schedule-form input,
.schedule-form textarea {
    width: 100%;
    padding: 10px;
    border: 1px solid #ced4da;
    border-radius: 4px;
}

.schedule-form button {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 10px;
    border-radius: 4px;
    cursor: pointer;
}

.schedule-list {
    margin-top: 20px;
}

.schedule-item {
    background-color: white;
    border: 1px solid #e0e0e0;
    padding: 15px;
    margin-bottom: 10px;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.delete-btn {
    background-color: #dc3545;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 4px;
    cursor: pointer;
}

.calendar-grid .today::before {
    content: '';
    position: absolute;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background-color: #868686;
    top: -5px;
    left: 50%;
    transform: translateX(-50%);
    z-index: -1;
}

.calendar-grid .today {
    color: white;
    position: relative;
    z-index: 1;
}

.day {
    width: 14%;
    height: 116px;
    border-bottom: 1px solid #EDF0F7;
}
</style>