<template>
  <div class="game" @mousemove="mousemove">
    <div class="indicator" id="indicator" v-text="isBaiting ? '' : 'CLICK TO CAST'"></div>

    <div class="bait-container" id="bait-container">
      <img class="bait" id="bait" src="@/assets/lure.png" alt />
      <div class="wave" id="wave" />
    </div>
    <div class="game-content">
      <div
        class="pond"
        id="pond"
        @mouseover="setIndicator(1)"
        @mouseout="setIndicator(0)"
        @click="startBait"
      />
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data: () => ({
    indicator: null,
    bait: null,
    baitContainer: null,
    gsap: null,
    pond: null,
    isBaiting: false,
    timeline: null
  }),

  mounted() {
    this.gsap = gsap;
    this.masterTimeline = gsap.timeline;
    this.initElements();
  },

  watch: {
    isBaiting(val) {
      this.gsap.to(this.pond, 1, {
        opacity: val ? 0 : 1,
        ease: val ? "power4.out" : "power4.in"
      });
    }
  },

  methods: {
    initElements() {
      this.indicator = document.getElementById("indicator");
      this.bait = document.getElementById("bait");
      this.pond = document.getElementById("pond");
      this.wave = document.getElementById("wave");
      this.baitContainer = document.getElementById("bait-container");
    },

    resetElements() {
      this.gsap.set([this.baitContainer, this.lure, this.wave], {
        clearProps: "all"
      });
    },

    animateWave() {
      const tl = gsap.timeline();

      tl.fromTo(
        this.wave,
        2,
        {
          width: "0px",
          height: "0px",
          opacity: 0.5
        },
        {
          y: "-=3px",
          ease: "Linear.easeNone",
          width: "50px",
          height: "10px",
          visibility: "visible",
          repeat: 2,
          opacity: 0
        }
      );
      return tl;
    },

    throwLure(e) {
      const tl = gsap.timeline();

      tl.fromTo(
        this.baitContainer,
        1.3,
        {
          top: e.pageY - 200,
          left: e.pageX - 25,
          scale: 0,
          visibility: "hidden"
        },
        {
          ease: "elastic.inOut(1, 0.75)",
          top: e.pageY,
          left: e.pageX - 25,
          scale: 1,
          visibility: "visible"
        }
      );

      return tl;
    },

    startBait(e) {
      if (this.isBaiting) return;

      let tl = gsap.timeline();

      this.isBaiting = true;

      tl.add(this.throwLure(e));
      tl.add(this.animateWave(), 1);

      this.floatLure();
    },

    floatLure() {
      this.resetElements();

      let vue = this;
      this.gsap.to(this.baitContainer, 1, {
        y: "-=3px",
        ease: "Linear.easeNone",
        yoyo: true,
        repeat: 6,
        onComplete: () => vue.catchFish()
      });
    },

    biteLure() {
      const tl = gsap.timeline();

      tl.to(this.baitContainer, 0.5, {
        ease: "power4.out",
        y: "+=10"
      });

      return tl;
    },

    getLure() {
      const tl = gsap.timeline();

      tl.to(this.baitContainer, 0.3, {
        ease: "power4.in",
        y: "-=100",
        opacity: 0
      });

      return tl;
    },

    catchFish() {
      let tl = gsap.timeline();
      let vue = this;

      tl.add(this.biteLure(), 0);
      tl.add(this.getLure(), 0.3);
      tl.then(x => {
        vue.isBaiting = false;
        vue.resetElements();
      });
    },

    setIndicator(scale) {
      this.gsap.to(this.indicator, 0.15, { scale, autoAlpha: 1 });
    },

    mousemove(e) {
      this.gsap.to(this.indicator, 0.2, {
        left: e.pageX + this.indicator.clientWidth / 2,
        top: e.pageY + this.indicator.clientHeight / 2
      });
    }
  }
};
</script>

<style lang="scss" scoped>
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

  .bait-container {
    position: absolute;
    z-index: 3;
    width: 50px;
    text-align: center;
    visibility: hidden;
    // border: 1px solid white;

    .bait {
      z-index: 3;
      max-width: 20px;
      // visibility: hidden;
    }

    .wave {
      z-index: 3;
      height: 5px;
      width: 10px;
      border-radius: 50%;
      border: 2px solid #fff;
      border-top: 0.5px;
      background: rgba(0, 0, 0, 0);
      margin: 0 auto;
      margin-top: -7px;
      visibility: hidden;
      // animation: wave 2s ease-out infinite;
    }
  }

  .game-content {
    position: relative;
    height: 500px;
    width: 800px;
    background: #0e3741;
    display: flex;
    justify-content: center;
    background-size: cover;

    .pond {
      position: absolute;
      height: 200px;
      width: 700px;
      background-color: rgba(0, 0, 0, 0);
      border-radius: 50%;
      bottom: 20px;
      transition: animation 1s;
      animation: neon 1.5s ease-in-out infinite alternate;
      // cursor: none;
    }
  }
}
</style>
