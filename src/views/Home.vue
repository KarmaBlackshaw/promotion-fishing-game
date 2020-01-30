<template>
  <div class="game">
    <div class="indicator" id="indicator">CLICK TO CAST</div>
    <img class="bait" id="bait" src="@/assets/floatBait.png" alt />
    <div class="game-content">
      <div class="pond" @mouseover="mouseover" @mouseout="mouseout" @click="throwBait"></div>
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data: () => ({
    indicator: null,
    bait: null,

    randomX: 0,
    randomY: 0,
    randomTime: 0,
    randomTime2: 0,
    randomAngle: 0
  }),

  async created() {
    gsap.config({
      force3D: false
    });
  },

  mounted() {
    this.indicator = document.getElementById("indicator");
    this.bait = document.getElementById("bait");
    window.addEventListener("mousemove", this.mousemove);

    // this.float(document.getElementById("bait"));
  },

  methods: {
    throwBait(e) {
      gsap.fromTo(
        this.bait,
        1.5,
        {
          ease: "elastic.out(1, 0.4)",
          css: {
            left: e.pageX,
            top: e.pageY - 200,
            scaleY: 0,
            scaleX: 0,
            visibility: "hidden"
          }
        },
        {
          ease: "elastic.out(1, 0.4)",
          css: {
            left: e.pageX,
            top: e.pageY,
            scaleY: 1,
            scaleX: 1,
            visibility: "visible"
          }
        }
      );

      this.float(this.bait);
    },

    mouseout() {
      gsap.to(this.indicator, 0.1, { scale: 0, autoAlpha: 1 });
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

    float(element) {
      gsap.set(element, { clearProps: "all" });

      this.randomX = this.random(5, 10);
      this.randomY = this.random(10, 15);
      this.randomTime = this.random(1, 3);
      this.randomTime2 = this.random(1, 3);
      this.randomAngle = this.random(4, 6);

      gsap.set(element, {
        x: this.randomX(-1),
        y: this.randomX(1),
        rotation: this.randomAngle(-1)
      });

      this.moveX(element, 1);
      this.moveY(element, -1);
      this.rotate(element, 1);
    },

    rotate(target, direction) {
      gsap.to(target, this.randomTime2(), {
        rotation: this.randomAngle(direction),
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

.game {
  display: flex;
  justify-content: center;
  user-select: none;

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

  .bait {
    position: absolute;
    z-index: 3;
    max-width: 20px;
    visibility: hidden;
  }

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
