<template>
  <div class="main-container">
    <button class="triangle-left" v-if="left" @click="setCurrentDaysLeftArrow"></button>
    <div class="table-container">
      <table class="table">
        <caption class="table-caption">
          {{ date.nameOfMonth + " " + date.year }}
        </caption>
        <thead>
          <tr>
            <th>Пн</th>
            <th>Вт</th>
            <th>Ср</th>
            <th>Чт</th>
            <th>Пт</th>
            <th>Сб</th>
            <th>Вс</th>
          </tr>
        </thead>
        <tbody>
          <tr class="table-line">
            <td class="circle" v-for="day in currentDays.slice(0, 7)" 
						:class="{ active: (active.day === day.day) && (active.month === day.month)}" :key="day.day">{{ day.day }}</td>
          </tr>
          <tr class="table-line">
						<td class="circle" v-for="day in currentDays.slice(7, 14)" 
						:class="{ active: (active.day === day.day) && (active.month === day.month)}" :key="day.day">{{ day.day }}</td>
          </tr>
          <tr class="table-line">
						<td class="circle" v-for="day in currentDays.slice(14, 21)" 
						:class="{ active: (active.day === day.day) && (active.month === day.month)}" :key="day.day">{{ day.day }}</td>
          </tr>
          <tr class="table-line">
						<td class="circle" v-for="day in currentDays.slice(21, 28)" :key="day.day" 
						:class="{ active: (active.day === day.day) && (active.month === day.month)}">{{ day.day }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <button class="triangle-right" v-if="right" @click="setCurrentDaysRightArrow"></button>
  </div>
</template>

<script>
export default {
  name: "Calendar",
  data() {
    return {
      date: {
        fullDate: "",
        year: "",
        month: "",
        nameOfMonth: "",
        today: "",
        dayOfWeek: "",
      },
      active: {
        day: "",
        month: "",
      },
      year: [
        { month: "Январь", count: 31, days: [] },
        { month: "Февраль", count: 28, days: [] },
        { month: "Март", count: 31, days: [] },
        { month: "Апрель", count: 30, days: [] },
        { month: "Май", count: 31, days: [] },
        { month: "Июнь", count: 30, days: [] },
        { month: "Июль", count: 31, days: [] },
        { month: "Август", count: 31, days: [] },
        { month: "Сентябрь", count: 30, days: [] },
        { month: "Октябрь", count: 31, days: [] },
        { month: "Ноябрь", count: 30, days: [] },
        { month: "Декабрь", count: 31, days: [] },
      ],
      currentDays: [],
    };
  },
  computed: {
    left() {
      if (
        this.date.fullDate.getMonth() === 11 &&
        this.date.fullDate.getFullYear() === 2020
      )
        return false;
      else return true;
    },
    right() {
      if (
        this.date.fullDate.getMonth() === 11 &&
        this.date.fullDate.getFullYear() === 2021
      )
        return false;
      else return true;
    },
  },
  methods: {
    fillDays(month) {
      month = month % 12
      let count = this.year[month].count
      for (let i = 0; i < count; i++) {
        this.year[month].days[i] = i + 1
      }
    },
    setDate() {
      this.date.fullDate = new Date();
      this.date.month = this.date.fullDate.getMonth()
      this.date.nameOfMonth = this.year[this.date.fullDate.getMonth()].month
      this.date.year = this.date.fullDate.getFullYear()
      this.date.today = this.date.fullDate.getDate()
      this.date.dayOfWeek = this.date.fullDate.getDay()
      this.active.day = this.date.today;
      this.active.month = this.date.month;
    },
    updateCurrentDays(curentDate) {
      const month = curentDate.getMonth()
      const day = curentDate.getDate()
      const dayOfWeek = curentDate.getDay()
      this.fillDays(month)

      const temp = [];

      // find days from monday
      let daysFromMonday = 0
      if (dayOfWeek === 0) daysFromMonday = 6
      else daysFromMonday = dayOfWeek - 1

      // case 1
      if (daysFromMonday > 0 && day <= daysFromMonday) {
        const lackOfDays = daysFromMonday + 1 - day
        this.fillDays(month - 1)
        for (let i = 0; i < lackOfDays; i++) {
          const m = this.year[month - 1]
          temp[i] = { day: m.days[m.count - lackOfDays + i], month: month - 1 }
        }
        for (let i = 0; i < 28 - lackOfDays; i++) {
          temp[i + lackOfDays] = { day: this.year[month].days[i], month: month }
        }
      }

      // case 2
      if (daysFromMonday >= 0 && day > daysFromMonday) {
        const firstDay = day - daysFromMonday
        const currentLength = this.year[month].count - firstDay
        for (let i = 0; i < 28; i++) {
          temp[i] = { day: this.year[month].days[i + firstDay - 1], month: month }
        }
        this.fillDays(month + 1);
        for (let i = 0; i < 28 - currentLength - 1; i++) {
          temp[i + currentLength + 1] = { day: this.year[(month + 1) % 12].days[i], month: (month + 1) % 12 }
        }
      }

      this.currentDays = temp;
    },
    setCurrentDays() {
      this.updateCurrentDays(this.date.fullDate)
    },
    setCurrentDaysLeftArrow() {
      const ms = this.date.fullDate.getTime();
      this.date.fullDate = new Date(ms - 86400000 * 7);
      this.updateCurrentDays(this.date.fullDate);
      this.date.nameOfMonth = this.year[this.date.fullDate.getMonth()].month
      this.date.year = this.date.fullDate.getFullYear();
    },
    setCurrentDaysRightArrow() {
      const ms = this.date.fullDate.getTime();
      this.date.fullDate = new Date(ms + 86400000 * 7);
      this.updateCurrentDays(this.date.fullDate);
      this.date.nameOfMonth = this.year[this.date.fullDate.getMonth()].month
      this.date.year = this.date.fullDate.getFullYear();
    },
  },
  beforeMount() {
    this.setDate()
    this.setCurrentDays()
  },
};
</script>

<style lang="less" scoped>
.main-container {
	font-family: "Roboto Condensed";
	display: flex;
	margin: 0 auto;
  width: 550px;
}
	.triangle-left {
		cursor: pointer;
		width: 0;
		height: 0;
		margin: 25% 30px;
		border: none; 
		outline: none;
		background-color: #ffffff;
		border-top: 25px solid transparent;
		border-bottom: 25px solid transparent;
		border-right: 40px solid #C1C1C1;
	}

	.triangle-right {
		cursor: pointer;
		width: 0;
		height: 0;
		margin: 25% 30px;
		border: none; 
		outline: none;
		background-color: #ffffff;
		border-top: 25px solid transparent;
		border-bottom: 25px solid transparent;
		border-left: 40px solid #C1C1C1;
	}

.table {
  margin: 0 auto;
}
	.table-caption {
		font-size: 20px;
		margin-top: 20px;
		padding-bottom: 10px;
	}
	.table-line {
		height: 2.5em;
		vertical-align: middle;
	}
	.circle {
		border: 0.15em solid #C1C1C1;
		border-radius: 50%;
		height: 2.5em;
		width: 2.5em;
		text-align: center;
	}
		.active {
			border-color: #0047FF;
		}
</style>
