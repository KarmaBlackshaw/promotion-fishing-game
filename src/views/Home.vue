<template>
  <div class="game" @mousemove="mousemove">
    <div class="indicator" id="indicator" v-text="isBaiting ? '' : 'CLICK TO CAST'"></div>

    <div class="bait-container" id="bait-container">
      <img class="bait" id="bait" src="@/assets/lure.png" alt />
      <div class="wave" id="wave" />
    </div>

    <div class="game-content">
      <div class="winning-effect" :class="points === 0 ? 'lose' : 'win'" id="winning-effect">
        <img
          :src="require(`@/assets/fishes/fish${points}.png`)"
          class="winning-effect__fish"
          id="winning-effect__fish"
          alt
        />
        <br />
        <span class="winning-effect__text" id="winning-effect__text">
          <h1 class="stroke-inner" id="stroke-inner">{{ points * 100 }} P0ints</h1>
          <h1 class="stroke-outer" id="stroke-outer">{{ points * 100 }} P0ints</h1>
          <h1 class="no-stroke" id="no-stroke">{{ points * 100 }} P0ints</h1>
        </span>
      </div>

      <img src="@/assets/Fishing-02.jpg" class="game-content__bg" alt />
      <!-- <div class="pond" id="pond" @mouseover="setIndicator(1)" @mouseout="setIndicator(0)" /> -->
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
    baitContainer: null,
    gsap: null,
    pond: null,
    isBaiting: false,
    timeline: null,
    points: 0
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

      this.points = Math.floor(Math.random() * 5);
      this.setWinningEffect(this.points);
      console.log(this.points);

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
        y: `-=3px`,
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

    setWinningEffect() {
      let strokeInner = document.getElementById("stroke-inner");
      let strokeOuter = document.getElementById("stroke-outer");
      let stroke = document.getElementById("no-stroke");

      const tl = gsap.timeline();
      const isWin = this.points > 0;

      tl.set(strokeOuter, {
        "-webkit-text-stroke": "12px #f2ece8"
      });
      tl.set(strokeInner, {
        "-webkit-text-stroke": `10px #${isWin ? "15488d" : "7c1b1a"}`,
        zIndex: 1
      });
      tl.set(stroke, {
        color: `#${isWin ? "71cede" : "f83534"}`,
        zIndex: 2
      });
    },

    catchFish() {
      let tl = gsap.timeline();
      let vue = this;

      tl.add(this.biteLure(), 0);
      tl.add(this.getLure(), 0.3);
      tl.then(x => {
        vue.showFish();
        vue.resetElements();
      });
    },

    showFish() {
      let effect = document.getElementById("winning-effect");

      this.gsap.fromTo(
        effect,
        2,
        {
          scale: 0,
          opacity: 0,
          visibility: "hidden"
        },
        {
          scale: 0.8,
          opacity: 1,
          ease: "elastic.out(1, 0.4)",
          visibility: "visible"
        }
      );
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
@import "../assets/fonts";

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

    .bait {
      z-index: 3;
      max-width: 20px;
    }

    .wave {
      z-index: 3;
      height: 5px;
      width: 10px;
      border-radius: 50%;
      border: 5px solid #fff;
      border-bottom: 2px;
      border-top: 0.5px;
      background: rgba(0, 0, 0, 0);
      margin: 0 auto;
      margin-top: -7px;
      visibility: hidden;
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

    .winning-effect {
      position: absolute;
      z-index: 1;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      border-radius: 50%;
      border: none;
      // win
      // width: 100%;
      // height: 100%;
      // background-image: url("../assets/doubleGlow.png");
      // background-size: cover;
      // end win

      // lose

      // lose end
      background-repeat: no-repeat;
      background-position: bottom;
      visibility: hidden;
      opacity: 0;

      &.lose {
        width: 40%;
        height: 40%;
        background-image: url("../assets/0_light.png");
        background-size: 50% 50%;
        background-color: rgba(248, 53, 52, 0.4);
        box-shadow: -1px 2px 70px 116px rgba(248, 53, 52, 0.4);
      }

      &.win {
        width: 100%;
        height: 100%;
        background-image: url("../assets/doubleGlow.png");
        background-size: cover;
      }

      .winning-effect__fish {
      }

      .winning-effect__text {
        font-family: "LuckiestGuy";
        position: relative;
        width: 100%;
        font-size: 1.5rem;

        h1 {
          position: absolute;
          left: 50%;
          transform: translateX(-50%);
          padding: 0;
          margin: 0;
        }
      }
    }

    .game-content__bg {
      width: 100%;
      height: 100%;
      display: block;
    }

    .pond {
      position: absolute;
      height: 130px;
      width: 700px;
      background-color: rgba(0, 0, 0, 0);
      border-radius: 50%;
      bottom: 100px;
      transition: animation 1s;
      animation: neon 1.5s ease-in-out infinite alternate;
      // cursor: none;
    }
  }
}
</style>
