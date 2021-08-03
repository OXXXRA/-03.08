<template lang="pug">
v-container
  v-btn(@click="swapLang()")
    v-icon mdi-translate
  calendar(@select="selectDay($event)" :lang="lang")
  p Выбрана дата: {{date}}
</template>
<script>
import Calendar from './components/Calendar.vue';

export default {
  components: { Calendar },
  data() {
    return {
      date: '',
      lang: 'Rus',
    };
  },
  methods: {
    selectDay(event) {
      this.date = event;
    },
    swapLang() {
      this.lang = this.lang === 'Rus' ? 'Eng' : 'Rus';
    },
  },
};
</script>
<style/>