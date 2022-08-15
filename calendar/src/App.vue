<template>
  <div class="calendar">
    <h1 class="title">{{ year }}년 {{ month }}월</h1>
    <button class="prevbutton" @click="calendarData(-1)">&lt;</button>
    <button class="nextbutton" @click="calendarData(1)">&gt;</button>
    <table class="date table">
      <thead>
      <th v-for="day in days" :key="day">{{ day }}</th>
      </thead>
      <tbody>
      <tr v-for="(date, a) in dates" :key="a">
        <td v-for="(day, b) in date" :key="b">
          {{ day }}
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      days: ["일", "월", "화", "수", "목", "금", "토"],
      dates: [],
    };
  },
  created() {
    //날짜데이터 받기
    const date = new Date();
    this.year = date.getFullYear();
    this.month = date.getMonth() + 1;
    this.today = date.getDate(); //오늘 날짜
    this.calendarData();
  },
  methods: {
    calendarData(mm) {
      if (mm < 0) {
        // 버튼 누르면 페이지 이동
        this.month -= 1;
      } else if (mm === 1) {
        this.month += 1;
      }
      if (this.month === 0) {
        // 작년 12월로 이동
        this.year -= 1;
        this.month = 12;
      } else if (this.month > 12) {
        // 내년 1월로 이동
        this.year += 1;
        this.month = 1;
      }
      //캘린더에 표시 할 내용
      const [monthFirstDay, monthLastDate, lastMonthLastDate] =
          this.FirstLastDate(this.year, this.month);
      this.dates = this.MonthOfDays(
          monthFirstDay,
          monthLastDate,
          lastMonthLastDate
      );
    },

    FirstLastDate(year, month) {
      const firstDay = new Date(year, month - 1, 1).getDay(); // 이번 달 시작 요일
      const lastDate = new Date(year, month, 0).getDate(); // 이번 달 마지막 날짜
      let lastYear = year;
      let lastMonth = month - 1;
      if (month === 1) {
        lastMonth = 12;
        lastYear -= 1;
      }
      const prevLastDate = new Date(lastYear, lastMonth, 0).getDate(); // 지난 달 마지막 날짜
      return [firstDay, lastDate, prevLastDate];
    },

    MonthOfDays(monthFirstDay, monthLastDate, prevMonthLastDate) {
      let day = 1;
      let prevDay = prevMonthLastDate - monthFirstDay + 1;
      const dates = [];
      let weekOfDays = [];
      while (day <= monthLastDate) {
        if (day === 1) {
          for (let j = 0; j < monthFirstDay; j += 1) {
            weekOfDays.push(prevDay);
            prevDay += 1;
          }
        }
        weekOfDays.push(day);
        if (weekOfDays.length === 7) {
          // 일주일 채우면
          dates.push(weekOfDays);
          weekOfDays = [];
        }
        day += 1;
      }
      const len = weekOfDays.length;
      if (len > 0 && len < 7) {
        for (let k = 1; k <= 7 - len; k += 1) {
          weekOfDays.push(k);
        }
      }
      if (weekOfDays.length > 0) dates.push(weekOfDays); // 남은 날짜 추가
      return dates;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 50px;
}
.prevbutton {
  position: relative;
  left: -250px;
  width: 30px;
  height: 30px;
  border: none;
  font-size: 20px;
}

.nextbutton {
  position: relative;
  left: 250px;
  width: 30px;
  height: 30px;
  border: none;
  font-size: 20px;
}
table {
  width: 100%;
  border-collapse: collapse;
}
th,
td {
  padding: 35px;
}
th {
  border-bottom: 1.2pt solid rgb(214, 214, 214);
}
td {
  border-bottom: 0.5pt solid rgb(214, 214, 214);
}

td:nth-child(1) {
  color: red;
}
td:nth-child(7) {
  color: blue;
}
</style>
