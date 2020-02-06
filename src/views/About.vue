<template>
  <div class="game">
    <div class="indicator" id="indicator" v-text="isBaiting ? '' : 'CLICK TO CAST'"></div>

    <div class="bait" id="bait">
      <center>
        <img src="@/assets/lure.png" class="lure" id="lure" alt />
        <div class="wave" id="wave"></div>
      </center>
    </div>
    <div class="game-content" @mousemove="mousemove">
      <div
        class="pond"
        id="pond"
        @mouseover="showIndicator(1)"
        @mouseout="showIndicator(0)"
        @click="throwBait"
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
    lure: null,
    wave: null,
    gsap: null,
    timeline: null,
    pond: null,
    isBaiting: false,
    animation: null
  }),

  async created() {
    this.gsap = gsap;
  },

  mounted() {
    this.initElements();

    this.timeline = gsap.timeline();
  },

  methods: {
    initElements() {
      this.wave = document.getElementById("wave");
      this.indicator = document.getElementById("indicator");
      this.bait = document.getElementById("bait");
      this.pond = document.getElementById("pond");
      this.lure = document.getElementById("lure");
    },

    async throwBait(e) {
      let vue = this;

      this.animation = this.timeline
        .set(this.lure, { opacity: 1, y: -5 }, 0)
        .fromTo(
          [this.lure, this.bait],
          1.3,
          {
            left: e.pageX,
            top: e.pageY - 200,
            scale: 0,
            opacity: 0,
            visibility: "hidden"
          },
          {
            ease: "elastic.inOut(1, 0.75)",
            left: e.pageX - 25, // 25: Half of lure's width
            top: e.pageY,
            scale: 1,
            opacity: 1,
            visibility: "visible"
          },
          0
        )
        .fromTo(
          this.wave,
          2,
          {
            y: "-5",
            width: "0px",
            height: "0px",
            border: "1px solid white",
            opacity: 1
          },
          {
            y: "-=5",
            ease: "Linear.easeNone",
            width: "50px",
            height: "10px",
            border: ".1px solid white",
            visibility: "visible",
            repeat: 2,
            opacity: 0
          },
          1
        )
        .to(
          this.lure,
          1,
          {
            y: "-=5px",
            ease: "Linear.easeNone",
            yoyo: true,
            repeat: 6
          },
          0
        )
        .to(
          this.lure,
          1,
          {
            y: 20,
            ease: "power4.out"
          },
          6
        )
        .to(
          this.lure,
          0.5,
          {
            y: -100,
            ease: "power4.in",
            opacity: 0,
            scale: 0
          },
          6.5
        )
        .then(x => vue.reset());
    },

    reset() {
      this.timeline = gsap.timeline();
      this.timeline.time(0);
    },

    showIndicator(scale) {
      this.gsap.to(this.indicator, 0.3, { scale, autoAlpha: 1 });
    },

    mousemove(e) {
      this.gsap.to(this.indicator, 0.2, {
        css: {
          left: e.pageX + this.indicator.clientWidth / 2,
          top: e.pageY + this.indicator.clientHeight / 2
        }
      });
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
    z-index: 2;
    pointer-events: none;
    animation: floating 0.7s infinite alternate;
    visibility: hidden;
    font-size: 1rem;
    font-weight: 700;
    color: red;
  }

  .bait {
    position: absolute;
    z-index: 2;
    width: 50px;

    .lure {
      width: 15px;
      margin: 0 auto;
      z-index: 5;
      visibility: hidden;
    }

    .wave {
      z-index: 3;
      height: 5px;
      width: 10px;
      border-radius: 50%;
      border: 1px solid #fff;
      border-top: 0.5px;
      background: rgba(0, 0, 0, 0);
      margin-top: -5px;
      visibility: hidden;
    }
  }

  .game-content {
    position: relative;
    height: 525px;
    width: 700px;
    background: #0e3741;
    display: flex;
    justify-content: center;
    // background-image: url("../assets/Fishing-02.jpg");
    background-size: contain;

    .pond {
      position: absolute;
      height: 150px;
      width: 600px;
      background-color: rgba(0, 0, 0, 0);
      border-radius: 50%;
      bottom: 100px;
      border-top: 2px;
      transition: animation 1s;
      animation: neon 1.5s ease-in-out infinite alternate;
      // cursor: none;
    }
  }
}
</style>
