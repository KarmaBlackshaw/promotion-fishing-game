<template>
  <div class="game">
    <div class="indicator" id="indicator">CLICK TO CAST</div>
    <div class="game-content">
      <div class="pond" @mouseover="mouseover" @mouseout="mouseout"></div>
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data: () => ({
    indicator: null,

    randomX: 0,
    randomY: 0,
    randomTime: 0,
    randomTime2: 0,
    randomAngle: 0
  }),

  mounted() {
    this.indicator = document.getElementById("indicator");
    window.addEventListener("mousemove", this.mousemove);
  },

  methods: {
    floatBait() {
      this.randomX = this.random(10, 20);
      this.randomY = this.random(20, 30);
      this.randomTime = this.random(3, 5);
      this.randomTime2 = this.random(5, 10);
      this.randomAngle = this.random(8, 12);

      gsap.set(this.indicator, {
        x: this.randomX(-1),
        y: this.randomX(1),
        rotation: this.randomAngle(-1)
      });

      this.moveX(this.indicator, 1);
      this.moveY(this.indicator, -1);
      this.rotate(this.indicator, 1);
    },

    mouseover() {
      gsap.to(this.indicator, 0.15, { scale: 1, autoAlpha: 1 });
    },

    mousemove(e) {
      gsap.to(this.indicator, 0.2, {
        css: {
          left: e.pageX + this.indicator.clientWidth / 2,
          top: e.pageY + this.indicator.clientHeight / 2
        }
      });
    },

    mouseout() {
      gsap.to(this.indicator, 0.1, { scale: 0, autoAlpha: 1 });
    },

    rotate(target, direction) {
      gsap.to(target, this.randomTime2(), {
        rotation: this.randomAngle(direction),
        // delay: randomDelay(),
        ease: "Sine.easeInOut",
        onComplete: this.rotate,
        onCompleteParams: [target, direction * -1]
      });
    },

    moveX(target, direction) {
      gsap.to(target, this.randomTime(), {
        x: this.randomX(direction),
        ease: "Sine.easeInOut",
        onComplete: this.moveX,
        onCompleteParams: [target, direction * -1]
      });
    },

    moveY(target, direction) {
      gsap.to(target, this.randomTime(), {
        y: this.randomY(direction),
        ease: "Sine.easeInOut",
        onComplete: this.moveY,
        onCompleteParams: [target, direction * -1]
      });
    },

    random(min, max) {
      const delta = max - min;
      return (direction = 1) => (min + delta * Math.random()) * direction;
    }
  }
};
</script>

<style lang="scss">
@import "../assets/keyframes";

$hookWidth: 100px;
$hookHeight: 50px;

.indicator {
  position: absolute;
  width: $hookWidth;
  height: $hookHeight;
  top: 50%;
  left: 50%;
  margin: (-$hookHeight) 0 0 (-$hookWidth);
  border-radius: 80%;
  backface-visibility: hidden;
  z-index: 5;
  pointer-events: none;
  animation: floating 0.7s ease-in-out infinite alternate;
  visibility: hidden;
  font-size: 1rem;
  font-weight: bold;
  color: white;
}

.game {
  display: flex;
  justify-content: center;

  .game-content {
    height: 500px;
    width: 800px;
    background: #0e3741;
    display: flex;
    justify-content: center;
    position: relative;

    .pond {
      position: absolute;
      height: 250px;
      width: 700px;
      background-color: rgba(0, 0, 0, 0);
      border-radius: 50%;
      bottom: 20px;
      animation: neon 1.5s ease-in-out infinite alternate;
      // cursor: none;
    }
  }
}
</style>
