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

  function numeralDecors(ctx, color, r) {
    var i = 0;
    ctx.fillStyle = color;
    while(i < 60) {
      var angle = 6 * Math.PI / 180;
      if(i == 0) angle = 0;
      ctx.beginPath();
      ctx.translate(r, r);
      ctx.rotate(angle);
      ctx.translate(-r, -r);
      ctx.translate(0, -r * 0.90);
      if(i % 5 == 0)
        ctx.arc(r, r, r * 0.025, 0, 2 * Math.PI, false);
      else
        ctx.arc(r, r, r * 0.01, 0, 2 * Math.PI, false);
      ctx.fill();
      ctx.translate(0, r * 0.90);
      ctx.closePath();
      i++
    }
  }

  function toTime(time) {
    return (new Date(`1970-01-01 ${time}`)).getTime();
  }

  function diffAsDegree(diff) {
    return Math.floor(diff / 60000) / 2;
  }

  function draw({ ctx, radius, start, end, backgroundColor, rangeColor,
    showDecor, decorColor }) {
    const r = radius / 2;
    const refMinutes = diffAsDegree(toTime(start) - toTime('00:00'));
    const minutes = diffAsDegree(Math.abs(toTime(end) - toTime(start)));

    ctx.clearRect(0, 0, radius, radius);
    circle(ctx, backgroundColor, r, 0, 360);
    circle(ctx, rangeColor, r, refMinutes, minutes);

    if(showDecor) {
      numeralDecors(ctx, decorColor, r);
    }
  }

  export default {
    props: {
      showDecor: {
        type: Boolean,
        default: false,
      },
      decorColor: {
        type: String,
        default: '#2c3e50',
      },
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
