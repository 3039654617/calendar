<template>
    <div class="app">
        <c-p :date='date' @chooseDate='chooseDate'/>
    </div>
</template>

<script>
import CP from './component.vue';
export default {
  components: { CP },
  data (){
      return {
          date: new Date(),
      }
  },
  methods: {
      chooseDate(date) {
          this.date = date;
      }
  }
}
</script>

<style scoped>
</style>