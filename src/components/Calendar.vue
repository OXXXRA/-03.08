<template lang="pug">
    v-card(outlined)
      v-card-title
        v-btn(small @click="changeMonth(false)")
          v-icon mdi-arrow-left
        p.pl-2 {{MONTHS[currentDate.getMonth()]}}
        v-btn.pl-2(small @click="changeMonth(true)")
          v-icon mdi-arrow-right
      v-card-text
        div.cell-row
          div.cell.ma-5(
            min-height="50px"
            min-width="50px"
            v-for="(day, i) in WEEK"
            :key="i"
            )
            h1 {{day}}
            div.pt-5(
              :style="{ color: checkSelected(cell) ? 'red' : '' }"
              v-for="(cell, index) in cells"
              :key="index"
            )
              p(
                v-if="checkDay(cell, day) && cell !== 0" 
                @click="selectDay(cell)"
                ) {{ cell }}
              p(v-if="checkNull(cell, day) && index === 0") ppp
</template>
<script>
import { getDaysInMonth } from 'date-fns';
import format from 'date-fns/format/index.js';
import { RUS_WEEK, ENG_WEEK, ENG_MONTHS, RUS_MONTHS } from '../constants';

export default {
  props: {
    lang: { type: String, required: true },
  },
  data() {
    return {
      WEEK: '',
      MONTHS: RUS_MONTHS,
      currentDate: new Date(),
      RUS_WEEK,
      RUS_MONTHS,
      ENG_WEEK,
      ENG_MONTHS,
      selected: null,
    };
  },
  mounted() {
    this.selected = this.currentDate;
  },
  computed: {
    firstDateOfMonth() {
      return format(new Date(this.currentDate.getFullYear(), this.currentDate.getMonth()), 'i');
    },
    cells() {
      let blanks = [];
      for (let i = 1; i <= getDaysInMonth(this.currentDate); i++) {
        blanks.push(i);
      }
      const firstDate = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth()).getDay();
      // const prevWeek = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() - 1, 0).getDate();
      for (let d = 1; d <= firstDate; d++) {
        blanks = [0, ...blanks];
      }
      return blanks;
    },
  },
  methods: {
    changeMonth(bool) {
      if (bool) this.currentDate = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() + 1);
      if (!bool) this.currentDate = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() -1);
    },
    selectDay(cell) {
      this.selected = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), cell);
      this.$emit('select', this.selected.toDateString());
    },
    checkNull(cell, day) {
      const date = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 1);
      return cell < date & this.WEEK.indexOf(day) < date.getDay();
    },
    checkSelected(cell) {
      if (!this.selected) return false;
      return Number(cell) === Number(this.selected.getDate()) && Number(this.selected.getMonth()) === Number(this.currentDate.getMonth());
    },
    checkDay(day, cell) {
      const date = new Date(this.currentDate).setDate(day);
      const result = new Date(date).getDay();
      return this.WEEK[result] === cell;
    },
  },
  watch: {
    lang: {
      immediate: true,
      handler(v) {
        if (v === 'Rus') {
          this.WEEK = this.RUS_WEEK;
          this.MONTHS = this.RUS_MONTHS;
        } else {
          this.WEEK = this.ENG_WEEK;
          this.MONTHS = this.ENG_MONTHS;
         
        }
      }
    }
  }
};
</script>
<style/>
<style scoped>
.cell-row {
  display: flex;
  flex-direction: row;
}
.cell-value {
  width: 100%;
  border-right: thin solid #c7c7c7;
  border-left: thin solid #c7c7c7;
  border-top: thin solid #c7c7c7;
  overflow: auto;
  border-radius: 5px;
}
.cell {
  width: calc(100% / 7);
  height: auto;
  min-height: 100px;
}
</style>