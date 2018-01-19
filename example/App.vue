<template>
  <div>
    <h2>Vue Analog Clock Range</h2>
    <VueAnalogClock start="12:00" end="13:00"></VueAnalogClock>
    <VueAnalogClock start="12:00" end="13:00" background-color='#ccc' range-color='#d864ef'></VueAnalogClock>
    <VueAnalogClock :radius="100" start="15:00" end="15:30" background-color='#ccc' range-color='#d864ef'></VueAnalogClock>
    <h3>Dynamic</h3>
    <VueAnalogClock :radius="100" start="08:30" :end="now" background-color='#ccc' range-color='#d864ef'></VueAnalogClock>
    <VueAnalogClock :radius="100" start="00:00" :end="nowFaster" background-color='#ccc' range-color='#d864ef'></VueAnalogClock>
    <span>00:00 to {{ nowFaster }}</span>
  </div>
</template>

<script>
  import VueAnalogClock from '../src/vue-analog-clock-range';
import { setInterval } from 'timers';
  export default {
    components: {
      VueAnalogClock,
    },
    data() {
      return {
        nowFaster: '00:00',
      };
    },
    mounted() {
      let h = 0;
      let m = 0;
      setInterval(() => {
        m += 1;
        m = m % 60;
        if (m === 0) {
          h += 1;
          h = h % 12;
        }
        this.nowFaster = `${h}:${m}`;
      }, 4);
    },
    computed: {
      now() {
        const date = new Date();
        const time = `${date.getHours()}:${date.getMinutes()}`;
      },
    },
  };
</script>

