<template>
  <div v-click-outside class="date-picker">
    <div class="input">
      <input
        type="text"
        :value="formatDate"
        @change="change"
        class="input-inner"
        placeholder="请选择"
      />
      <span class="icon">
        <i class="iconfont icon-icon-p_rili icon-icon"></i>
      </span>
    </div>
    <div v-if="isVisiable" class="panel">
      <div class="panel-top">
        <span @click="preYear" class="iconfont icon-shuangjiantouzuo"></span>
        <span @click="preMonth" class="iconfont icon-fanhui"></span>
        <span>{{ formatSelectedDay.year }}年</span>
        <span>{{ formatSelectedDay.month + 1 }}月</span>
        <span @click="nextMonth" class="iconfont icon-gengduo"></span>
        <span @click="nextYear" class="iconfont icon-shuangjiantouyou"></span>
      </div>
      <div class="panel-content">
        <div class="panel-content-head">
          <span class="cell" v-for="i in panelToptext" :key="i">
            {{ i }}
          </span>
        </div>
        <div v-for="i in 6" :key="i">
          <span
            v-for="j in 7"
            :key="j"
            class="cell cell-day"
            @click="selectDay(visiableDays[isIndex(i, j)])"
            :class="[
              isNotCurrMonth(visiableDays[isIndex(i, j)])
                ? 'isNotCurrMonth'
                : '',
              isSelectedDay(visiableDays[isIndex(i, j)]) ? 'isSelectedDay' : '',
            ]"
          >
            {{ visiableDays[isIndex(i, j)].getDate() }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as utils from "../utils";

export default {
  name: "DatePicker",
  directives: {
    clickOutside: {
      bind(el, bindings, vnode) {
        console.log(bindings);
        const handler = (e) => {
          const { isVisiable } = vnode.context;
          if (el.contains(e.target)) {
            if (!isVisiable) {
              vnode.context.focus();
            }
          } else {
            if (isVisiable) {
              vnode.context.blur();
            }
          }
        };
        el.handler = handler;
        document.addEventListener("click", handler);
      },
      unbind(el) {
        document.removeEventListener("click", el.handler);
      },
    },
  },
  props: {
    value: {
      type: Date,
      default: () => new Date(),
    },
  },
  data() {
    return {
      isVisiable: true,
      selectedDay: this.value,
      panelToptext: ["日", "一", "二", "三", "四", "五", "六"],
    };
  },
  computed: {
    formatDate() {
      let { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      return `${year}-${month + 1}-${day}`;
    },
    visiableDays() {
      if (!this.selectedDay) return;
      const { year, month } = utils.getYearMonthDay(this.selectedDay);
      const currFirstDay = utils.getDate(year, month, 1);
      const weekday = currFirstDay.getDay();
      const startDay = currFirstDay - weekday * 60 * 60 * 1000 * 24;
      const arr = [];
      for (let i = 0; i < 42; i++) {
        arr.push(new Date(startDay + i * 60 * 60 * 1000 * 24));
      }
      return arr;
    },
    formatSelectedDay() {
      const { year, month } = utils.getYearMonthDay(this.selectedDay);
      return { year, month };
    },
  },
  methods: {
    isIndex(i, j) {
      return (i - 1) * 7 + j - 1;
    },
    focus() {
      this.isVisiable = true;
    },
    blur() {
      this.isVisiable = false;
    },
    isNotCurrMonth(date) {
      const { year, month } = utils.getYearMonthDay(this.selectedDay);
      const { year: y, month: m } = utils.getYearMonthDay(date);
      return year !== y || month !== m;
    },
    change(e) {
      let val = e.target.value;
      let date = new Date(e.target.value);
      if (!e.target.value) {
        this.selectedDay = new Date();
        this.$emit("change", "");
        return;
      }

      if (!utils.isValidDate(date)) {
        return;
      }

      if (date) {
        this.selectedDay = date;
        this.$emit("change", val);
      }
    },
    isSelectedDay(date) {
      const { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      const { year: y, month: m, day: d } = utils.getYearMonthDay(date);
      return year === y && month === m && day === d;
    },
    selectDay(day) {
      this.selectedDay = day;
    },
    preMonth() {
      let { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      month--;
      this.selectedDay = utils.getDate(year, month, day);
    },
    nextMonth() {
      let { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      month++;
      this.selectedDay = utils.getDate(year, month, day);
    },
    preYear() {
      let { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      year--;
      this.selectedDay = utils.getDate(year, month, day);
    },
    nextYear() {
      let { year, month, day } = utils.getYearMonthDay(this.selectedDay);
      year++;
      this.selectedDay = utils.getDate(year, month, day);
    },
  },
};
</script>

<style scoped >
.date-picker {
  width: 280px;
  height: 40px;
  user-select: none;
}
.panel-top {
  height: 50px;
  line-height: 50px;
  display: flex;
  justify-content: space-between;
  cursor: pointer;
}
.panel-content {
  width: auto;
}
.panel-content-head {
  border-bottom: 1px solid #f5f5f5;
}
.cell {
  display: inline-block;
  text-align: center;
  width: 40px;
  height: 40px;
  line-height: 40px;
  box-sizing: border-box;
  border: 1px solid #fff;
  cursor: pointer;
}
.cell-day:hover {
  border: 1px solid #f5f5f5;
}
.isNotCurrMonth {
  color: gray;
}
.isSelectedDay {
  background-color: lightcoral;
  color: #fff;
}
.input {
  position: relative;
}
.input .icon {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  left: 10px;
  height: 100%;
  display: inline-block;
}
.icon-icon {
  font-style: normal;
  font-weight: 400;
  font-variant: normal;
  text-transform: none;
  line-height: 1;
  vertical-align: baseline;
  display: inline-block;
  line-height: 34px;
}
.input-inner {
  background-color: #fff;
  background-image: none;
  border-radius: 4px;
  border: 1px solid #dcdfe6;
  box-sizing: border-box;
  color: #606266;
  display: inline-block;
  font-size: inherit;
  height: 36px;
  line-height: 36px;
  outline: none;
  padding: 0 36px;
  transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  width: 100%;
}
</style>
