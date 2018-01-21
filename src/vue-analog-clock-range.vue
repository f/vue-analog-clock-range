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
  //  start draw clock //
  function MakeField(ctx, radius, color, backgroundColor, borderColor , gradColor , boxColor) {
    var grad;
    ctx.beginPath();
    ctx.arc(0, 0, radius, 0, 2*Math.PI);
    ctx.fillStyle = backgroundColor;
    ctx.fill();
    grad = ctx.createRadialGradient(0,0,radius*0.95, 0,0,radius*1.05);
    grad.addColorStop(0, gradColor);
    grad.addColorStop(0.5, boxColor);
    grad.addColorStop(1, borderColor);
    ctx.strokeStyle = grad;
    ctx.lineWidth = radius*0.1;
    ctx.stroke();
    ctx.beginPath();
    ctx.arc(0, 0, radius*0.1, 0, 2*Math.PI);
    ctx.fillStyle = color;
    ctx.fill();
  }

  function MakeNumbers(ctx, radius) {
    var ang;
    var clock;
    ctx.font = "bold "+radius*0.18 + "px arial";
    ctx.textBaseline="middle";
    ctx.textAlign="center";
    for(clock = 1; clock < 13; clock++){
      ang = clock * Math.PI / 6;
      ctx.rotate(ang);
      ctx.translate(0, -radius*0.7);
      ctx.rotate(-ang);
      ctx.fillText(clock.toString(), 0, 0);
      ctx.rotate(ang);
      ctx.translate(0, radius*0.7);
      ctx.rotate(-ang);
    }
  }

  function MakeGrad(ctx, radius){
      var now = new Date();
      var hour = now.getHours();
      var minute = now.getMinutes();
      var second = now.getSeconds();
      // draw hour
      hour=hour%12;
      hour=(hour*Math.PI/6)+
      (minute*Math.PI/(6*60))+
      (second*Math.PI/(360*60));
      makeTime(ctx, hour, radius*0.5, radius*0.07);
      // draw minute
      minute=(minute*Math.PI/30)+(second*Math.PI/(30*60));
      makeTime(ctx, minute, radius*0.65, radius*0.07);
      //  draw second
      second=(second*Math.PI/30);
      makeTime(ctx, second, radius*0.9, radius*0.02);
  }

  function makeTime(ctx, pos, length, width) {
      ctx.beginPath();
      ctx.lineWidth = width;
      ctx.lineCap = "round";
      ctx.moveTo(0,0);
      ctx.rotate(pos);
      ctx.lineTo(0, -length);
      ctx.stroke();
      ctx.rotate(-pos);
  }

function MakeClock({ ctx, radius, backgroundColor, rangeColor, borderColor, gradColor, boxColor }){
  let r = radius / 2;
  ctx.translate(r,r);
  r = r * 0.90;
  MakeField(ctx, r, rangeColor, backgroundColor, borderColor, gradColor, boxColor);
  MakeNumbers(ctx, r);
  MakeGrad(ctx, r);
}
// finish draw clock  //
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
      isNumeric: {
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
      borderColor : {
        type: String,
        default: 'transparent',
      },
      gradColor : {
        type: String,
        default : '#cccccc',
      },
      boxColor: {
        type: String,
        default: 'transparent',
      }
    },
    computed: {
      ctx() {
        return this.$refs.canvas.getContext('2d');
      },
    },
    mounted() {
      if (this.isNumeric) {
        MakeClock(this);
        let specialRadius = this.radius/2*0.9
        setInterval(()=> {
          MakeField(this.ctx, specialRadius , this.rangeColor, this.backgroundColor, this.borderColor , this.gradColor ,this.boxColor);
          MakeGrad(this.ctx, specialRadius);
          MakeNumbers(this.ctx, specialRadius);
        },1000)
      } else{
        draw(this);
      }
    },
    watch: {
      start() { if(!this.isNumeric) { draw(this); }},
      end() { if(!this.isNumeric) {draw(this);} },
    },
  };
</script>
