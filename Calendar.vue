<script setup>
import dayjs from 'dayjs';

const currentDate = ref(dayjs());

const currentEvents = ref([]);

const events = [
  { id: 1, name: "ミーティング", start: "2024-11-01", end:"2024-11-01", color:"blue"},
  { id: 2, name: "イベント", start: "2024-11-02", end:"2024-11-03", color:"limegreen"},
  { id: 3, name: "会議", start: "2024-11-06", end:"2024-11-06", color:"deepskyblue"},
  { id: 4, name: "有給", start: "2024-11-08", end:"2024-11-08", color:"dimgray"},
  { id: 5, name: "海外旅行", start: "2024-11-08", end:"2024-11-11", color:"navy"},
  { id: 6, name: "誕生日", start: "2024-11-16", end:"2024-11-16", color:"orange"},
  { id: 7, name: "イベント", start: "2024-11-12", end:"2024-11-15", color:"limegreen"},
  { id: 8, name: "出張", start: "2024-11-12", end:"2024-11-13", color:"teal"},
  { id: 9, name: "客先訪問", start: "2024-11-14", end:"2024-11-14", color:"red"},
  { id: 10, name: "パーティ", start: "2024-11-15", end:"2024-11-15", color:"royalblue"},
  { id: 12, name: "ミーティング", start: "2024-11-18", end:"2024-11-19", color:"blue"},
  { id: 13, name: "イベント", start: "2024-11-21", end:"2024-11-21", color:"limegreen"},
  { id: 14, name: "有給", start: "2024-11-20", end:"2024-11-20", color:"dimgray"},
  { id: 15, name: "イベント", start: "2024-11-25", end:"2024-11-28", color:"limegreen"},
  { id: 16, name: "会議", start: "2024-11-21", end:"2024-11-21", color:"deepskyblue"},
  { id: 17, name: "旅行", start: "2024-11-23", end:"2024-11-24", color:"navy"},
  { id: 18, name: "ミーティング", start: "2024-11-28", end:"2024-11-28", color:"blue"},
  { id: 19, name: "会議", start: "2024-11-12", end:"2024-11-12", color:"deepskyblue"},
  { id: 20, name: "誕生日", start: "2024-11-30", end:"2024-11-30", color:"orange"},
];

const getStartDate = () => {
    let date = dayjs(currentDate.value).startOf("month");
    const youbiNum = date.day();
    return date.subtract(youbiNum, "days");
}

const getEndDate = () => {
    let date = dayjs(currentDate.value).endOf("month");
    const youbiNum = date.day();
    return date.add(6 - youbiNum, "days");
}

const getCalendar = () => {
    let calendarDate = getStartDate();
    const endDate = getEndDate();
    const weekNumber = Math.ceil(endDate.diff(calendarDate, "days") / 7);

    let calendars = [];
    for (let week = 0; week < weekNumber; week++) {
      let weekRow = [];
      for (let day = 0;  day < 7; day++) {
        let dayEvents = getDayEvents(calendarDate);
        weekRow.push({
          date: calendarDate.get("date"),
          month: calendarDate.format("YYYY-MM"),
          dayEvents
        });
        calendarDate = calendarDate.add(1, "days");
      }
      calendars.push(weekRow);
    }
    console.log(calendars);
    return calendars;
}

onMounted(() => {
})

const displayMonth = computed(() => {
    return currentDate.value.format('YYYY[年]M[月]');
});

const calendars = computed(() => {
    return getCalendar();
});

const currentMonth = computed(() => {
  return currentDate.value.format('YYYY-MM');
});

const nextMonth = () => {
    currentDate.value = dayjs(currentDate.value).add(1, "month");
};

const prevMonth = () => {
    currentDate.value = dayjs(currentDate.value).subtract(1, "month");
};

const youbi = (dayIndex) => {
  const week = ["日", "月", "火", "水", "木", "金", "土"];
  return week[dayIndex];
};

const getDayEvents = (date) => {
  return events.filter(event => {
    let startDate = dayjs(event.start).format('YYYY-MM-DD')
    let endDate = dayjs(event.end).format('YYYY-MM-DD')
    let Date = date.format('YYYY-MM-DD')
    if(startDate <= Date && endDate >= Date) return true;
  });
}

const clickEvent = (events) => {
//   events.map((event) => {
//     currentEvents.value.push(event);
//     console.log(event.name);
//   });
currentEvents.value = events;
  currentEvents.value.map((event) => {
    console.log(event.name);
  })  
}
</script>

<template>
    <div class="all">
        <div class="content">
      <div class="button-area">
        <v-btn @click="prevMonth" class="button" color="secondary">先月</v-btn>
        <v-btn class="button" color="secondary">{{ displayMonth }}</v-btn>
        <v-btn @click="nextMonth" class="button" color="secondary">来月</v-btn>
      </div>
      <div class="calendar">
        <div class="calendar-weekly">
            <div class="calendar-youbi" v-for="n in 7" :key="n">
                {{ youbi(n-1) }}
            </div>
        </div>
        <div 
          class="calendar-weekly"
          v-for="(week, index) in calendars"
          :key="index"
        >
          <div
            class="calendar-daily"
            :class="{outside: currentMonth !== day.month}"
            v-for="(day, index) in week"
            :key="index"
          >
            <div class="calendar-day">
              {{ day.date }}
            </div>
            <div class="calendar-event">
                <div v-if="day.dayEvents.length > 0" @click="clickEvent(day.dayEvents)" >◯</div>
                <div v-else>-</div>
            </div>
            <!-- <div v-for="dayEvent in day.dayEvents" :key="dayEvent.id" >
                <div 
                class="calendar-event"
                :style="`background-color:${dayEvent.color}`" >
                    {{ dayEvent.name }}
                </div>
            </div> -->
          </div>
        </div>
      </div>
    </div>
    <div class="display-event">
        <div v-if="currentEvents.length > 0">
            <div v-for="(event, index) in currentEvents" :key="index">
                <div>日付</div>
                <div >{{ event.id }}</div>
                <div >{{ event.start }}</div>
                <div >{{ event.end }}</div>
                <div >{{ event.name }}</div>
            </div>            
        </div>
    </div>
    </div>
  </template>

<style>
.all{
  flex:1;
}
.display-event{
    border:5px solid #E0E0E0;
  margin:2em auto;
  width:300px;
}
.content{
  margin:2em auto;
  width:900px;
}
.button-area{
  margin:0.5em 0;
  text-align: center;
}
.button{
  padding:4px 8px;
  margin-right:8px;
}
.calendar{
  max-width:900px;
  border-top:1px solid #E0E0E0;
  font-size:0.8em;
}
.calendar-weekly{
  display:flex;
  border-left:1px solid #E0E0E0;
  /* background-color: black; */
}
.calendar-daily{
  flex:1;
  /* min-height:125px; */
  border-right:1px solid #E0E0E0;
  margin-right:-1px;
  text-align: center;
}
.calendar-day{
    border-bottom:1px solid #E0E0E0;
    min-height:62.5px;
}
.calendar-event{
    border-bottom:1px solid #E0E0E0;
    min-height:62.5px;
}
.calendar-youbi{
  flex:1;
  border-right:1px solid #E0E0E0;
  border-bottom:1px solid #E0E0E0;
  margin-right:-1px;
  text-align:center;
}
.outside{
  background-color: #f7f7f7;
}
</style>