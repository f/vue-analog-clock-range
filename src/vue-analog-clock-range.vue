<template>
  <canvas ref='canvas' :width='radius' :height='radius'></canvas>
</template>

<script>
  function circle(ctx, color, r, start, angle) {
    const starts = (((start - 90) / 360) * (2 * Math.PI));
    const ends = starts + ((angle / 360) * (2 * Math.PI));
    ctx.fillStyle = color;
    ctx.moveTo(r, r);
    ctx.beginPath();
    ctx.arc(r, r, r, starts, ends, false);
    ctx.lineTo(r, r);
    ctx.fill();
  }

  function toTime(time) {
    return (new Date(`1970-01-01 ${time}`)).getTime();
  }

  function diffAsDegree(diff) {
    return Math.floor(diff / 60000) / 2;
  }

  function draw({ ctx, radius, start, end, backgroundColor, rangeColor }) {
    const r = radius / 2;
    const refMinutes = diffAsDegree(toTime(start) - toTime('00:00'));
    const minutes = diffAsDegree(Math.abs(toTime(end) - toTime(start)));

    ctx.clearRect(0, 0, radius, radius);
    circle(ctx, backgroundColor, r, 0, 360);
    circle(ctx, rangeColor, r, refMinutes, minutes);
  }

  export default {
    props: {
      start: {
        type: String,
      },
      end: {
        type: String,
      },
      backgroundColor: {
        type: String,
        default: '#f5f5f5',
      },
      rangeColor: {
        type: String,
        default: '#cccccc',
      },
      radius: {
        type: Number,
        default: 200,
      },
    },
    computed: {
      ctx() {
        return this.$refs.canvas.getContext('2d');
      },
    },
    mounted() {
      draw(this);
    },
    watch: {
      start() { draw(this); },
      end() { draw(this); },
    },
  };
</script>
