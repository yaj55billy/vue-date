<template>
  <div class="home">
    <div>
      <img alt="Vue logo" src="../assets/logo.png" />
    </div>
    <div>西元 {{ calendar.year }} 年 {{ calendar.month }} 月</div>
    <div>民國 {{ rocYear }} 年</div>

    <div>
      <button type="button" @click="adjustYear(-1)">上一年</button>
      <button type="button" @click="adjustMonth(-1)">上個月</button>
      <button type="button" @click="setToday">今天</button>
      <button type="button" @click="adjustMonth(1)">下個月</button>
      <button type="button" @click="adjustYear(1)">下一年</button>
    </div>
    <div class="calendar">
      <ul class="week-head">
        <li>日</li>
        <li>一</li>
        <li>二</li>
        <li>三</li>
        <li>四</li>
        <li>五</li>
        <li>六</li>
      </ul>
      <div class="week-body">
        <div
          v-for="item in monthData"
          :key="item"
          class="week-body__list"
          :class="{ notMonth: item.month !== calendar.month }"
        >
          <div>
            <div
              :class="{
                today:
                  item.year === today.year &&
                  item.month === today.month &&
                  item.date === today.date,
              }"
            >
              {{ item.date }}
            </div>
          </div>
        </div>
        <!-- <div v-for="i in 7" :key="i" class="week-body__list">
          <div v-for="j in 6" :key="j">
            {{ i }}
            {{ j }}
            {{ monthData[()] }}
          </div>
        </div> -->
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  data() {
    return {
      today: {
        year: 0,
        month: 0,
        date: 0,
        day: 0, // 星期幾
      },
      calendar: {
        year: 0,
        month: 0,
        date: 0,
        day: 0, // 星期幾
      },
    };
  },
  mounted() {
    this.setToday();
  },
  methods: {
    setToday() {
      const date = new Date();
      this.today.year = this.calendar.year = date.getFullYear();
      this.today.month = this.calendar.month = date.getMonth() + 1; // 0~11
      this.today.date = this.calendar.date = date.getDate();
      this.today.day = this.calendar.day = date.getDay();
    },
    adjustYear(para) {
      this.calendar.year += para;
    },
    adjustMonth(para) {
      if (this.calendar.month > 12) {
        // Next
        this.calendar.month = 1;
        this.adjustYear(1);
      } else if (this.calendar.month < 1) {
        // Prev
        this.calendar.month = 12;
        this.adjustYear(-1);
      } else {
        this.calendar.month += para;
      }
    },
  },
  computed: {
    monthFirst() {
      // 這個月的第一天
      const date = new Date(this.calendar.year, this.calendar.month - 1, 1);
      // this.calendar.month - 1 要記得 minus 1
      // console.log(date.getDay());
      return date.getDay();
    },
    monthFormFirst() {
      // 表格的第一天
      const date = new Date(
        this.calendar.year,
        this.calendar.month - 1,
        1 - this.monthFirst
      );

      return {
        year: date.getFullYear(),
        month: date.getMonth(),
        date: date.getDate(),
        day: date.getDay(),
      };
    },
    monthData() {
      // 整理出來的資料
      const monthData = [];
      for (let i = 0; i < 42; i++) {
        let date = new Date(
          this.monthFormFirst.year,
          this.monthFormFirst.month,
          this.monthFormFirst.date + i
        );
        monthData.push({
          year: date.getFullYear(),
          month: date.getMonth() + 1, // 這個是要 return 到畫面上的，所以加個 1
          date: date.getDate(),
          day: date.getDay(),
        });
      }
      return monthData;
    },
    rocYear() {
      return this.calendar.year - 1911;
    },
    // judgeToday() {
    //   // 判斷是否為今日，拿 today 出來對比
    //   let str;
    //   this.monthData.forEach((item) => {
    //     if (
    //       item.year === this.today.year &&
    //       item.month === this.today.month &&
    //       item.date === this.today.date
    //     ) {
    //       str = `${item.year}/${item.month}/${item.date}`;
    //       // return `${item.year}/${item.month}/${item.date}`;
    //     }
    //   });
    //   return str;
    // },
    // judgeMonth() {},
  },
};

/*
  Vue 萬年曆
  - 資料定義，有什麼資料是我們需要用的 ? (今天與切換)
  - mounted 初始化資料 new Date()
  - 思考切換功能 (下一年、下個月、上一年、上個月、今天)
  - 實作切換功能，並且注意範圍
  - 資料的部分、功能處理後 >> 畫面
  - 中間四周 + 頭尾兩周 (大多的日曆) >> 應如果每月的 1 號是星期六 (畫面先渲染)
  - 要知道這個月的第一天是什麼 ? 並且推算出這個月欄位的第一格是什麼
  - 從第一格跑迴圈渲染出 42 格的日期資料 v-for render
  - 調整畫面 (class: 當天、非當月的狀況 ... )
  - 民國 = 西元 - 1911
*/
</script>

<style lang="scss">
.calendar {
  margin: 30px;
}

.week-head {
  list-style: none;
  display: flex;
  li {
    flex-grow: 1;
  }
}
.week-body {
  display: flex;
  flex-wrap: wrap;
  border-top: solid 1px #ccc;
  border-right: solid 1px #ccc;
  &__list {
    $full: 100%;
    width: $full/7;
    flex-grow: 1;
    position: relative;

    &.notMonth {
      background-color: #eee;
    }

    > div {
      height: 90px;
      line-height: 30px;
      border-left: solid 1px #ccc;
      border-bottom: solid 1px #ccc;
      position: relative;

      > div {
        position: absolute;
        top: 0;
        right: 0;
        z-index: 1;
        border-left: solid 1px #ccc;
        border-bottom: solid 1px #ccc;
        width: 30px;
        height: 30px;
        &.today {
          background-color: #000;
          color: #fff;
        }
      }
    }
  }
}
</style>
