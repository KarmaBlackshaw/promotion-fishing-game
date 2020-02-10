<template>
  <div class="game" @mousemove="mousemove">
    <div class="indicator" id="indicator">
      <img src="@/assets/cast.png" class="indicator__text" alt />
      <img src="@/assets/arrow.png" class="indicator__arrow" id="indicator__arrow" alt />
      <img src="@/assets/target.png" class="indicator__target" alt />
    </div>

    <div class="bait-container" id="bait-container">
      <img class="bait" id="bait" src="@/assets/lure.png" alt />
      <div class="wave" id="wave" />
    </div>

    <div class="game-content">
      <div class="game-content__title-container">
        <img src="@/assets/title-1.png" id="title-1" class="title-1" alt />
        <img src="@/assets/title-2.png" id="title-2" class="title-2" alt />
      </div>

      <img src="@/assets/Fishing-02.jpg" class="game-content__bg" id="game-content__bg" alt />

      <div class="winning-effect" id="winning-effect">
        <img
          src="@/assets/0_light.png"
          v-if="points === 0"
          class="winning-effect__glow"
          id="glow--lose"
          alt
        />
        <img
          src="@/assets/glow1.png"
          v-if="points > 0"
          class="winning-effect__glow"
          id="glow--vertical"
          alt
        />
        <img
          src="@/assets/glow2.png"
          v-if="points > 0"
          class="winning-effect__glow"
          id="glow--circular"
          alt
        />
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

      <!-- <div
        class="pond"
        id="pond"
        @mouseover="setIndicator(1)"
        @mouseout="setIndicator(0)"
      />-->
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
    titleOne: null,
    titleTwo: null,

    isBaiting: false,

    timeline: null,
    points: 0,

    config: {
      floatDuration: 0.75,
      floatHeight: "-=3px",
      biteLureDepth: "+=10",
      biteLureDuration: 0.5,
      resultBackgroundBrightness: 80
    }
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
      this.gsap.to(this.indicator, 0.15, {
        scale: val ? 0 : 1,
        autoAlpha: 1
      });
    }
  },

  methods: {
    initElements() {
      this.indicator = document.getElementById("indicator");
      this.pond = document.getElementById("pond");
      this.wave = document.getElementById("wave");
      this.titleOne = document.getElementById("title-1");
      this.titleTwo = document.getElementById("title-2");
      this.baitContainer = document.getElementById("bait-container");
    },

    resetElements() {
      this.gsap.set([this.baitContainer, this.lure, this.wave], {
        clearProps: "all"
      });
    },

    setWinningEffect() {
      const strokeInner = document.getElementById("stroke-inner");
      const strokeOuter = document.getElementById("stroke-outer");
      const stroke = document.getElementById("no-stroke");
      const effect = document.getElementById("winning-effect");

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

      if (isWin) {
        tl.set(effect, {
          width: "40%",
          height: "40%",
          backgroundSize: "50% 50%"
        });
      } else {
        tl.set(effect, {
          width: "100%",
          height: "100%",
          backgroundSize: "cover"
        });
      }
    },

    startBait__animateWave() {
      const tl = gsap.timeline();

      tl.to(this.wave, this.config.floatDuration * 2, {
        y: "-=3px",
        ease: "Linear.easeNone",
        width: "50px",
        height: "10px",
        visibility: "visible",
        repeat: 2,
        opacity: 0
      });
      return tl;
    },

    startBait__throwLure(e) {
      const tl = gsap.timeline();

      tl.fromTo(
        this.baitContainer,
        1.3,
        {
          top: e.pageY - 200,
          left: e.pageX - 25
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

    startBait__switchTitle() {
      const tl = gsap.timeline();

      tl.to(
        this.titleTwo,
        1,
        {
          opacity: 0,
          ease: "power4.out"
        },
        0
      ).to(
        this.titleOne,
        2,
        {
          opacity: 1,
          ease: "power4.in",
          visibility: "visible"
        },
        0
      );

      return tl;
    },

    startBait(e) {
      if (this.isBaiting) return;

      this.points = Math.floor(Math.random() * 5);
      this.setWinningEffect(this.points);

      let tl = gsap.timeline();

      this.isBaiting = true;

      tl.add(this.startBait__switchTitle(), 0);
      tl.add(this.startBait__throwLure(e), 0);
      tl.add(this.startBait__animateWave(), 1);

      this.floatLure();
    },

    floatLure() {
      this.resetElements();

      let vue = this;
      this.gsap.to(this.baitContainer, this.config.floatDuration, {
        y: this.config.floatHeight,
        ease: "Linear.easeNone",
        yoyo: true,
        repeat: 6,
        onComplete: () => vue.catchFish()
      });
    },

    catchFish__biteLure() {
      const tl = gsap.timeline();

      tl.to(this.baitContainer, this.config.biteLureDuration, {
        ease: "power4.out",
        y: this.config.biteLureDepth
      });

      return tl;
    },

    catchFish__getLure() {
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

      tl.add(this.catchFish__biteLure(), 0);
      tl.add(this.catchFish__getLure(), 0.3);
      tl.then(x => {
        vue.showResult();
        vue.resetElements();
      });
    },

    showResult__darkenBackground() {
      const tl = gsap.timeline();
      const background = document.getElementById("game-content__bg");

      tl.fromTo(
        background,
        0.5,
        {
          filter: "brightness(100%)"
        },
        {
          filter: `brightness(${this.config.resultBackgroundBrightness}%)`,
          ease: "linear.easeNone"
        }
      );

      return tl;
    },

    showResult__setEffectVisibility() {
      const tl = gsap.timeline();
      const effect = document.getElementById("winning-effect");

      tl.fromTo(
        effect,
        1,
        {
          visibility: "hidden"
        },
        {
          visibility: "visible"
        }
      );

      return tl;
    },

    showResult__showFish() {
      const tl = gsap.timeline();
      const winningFish = document.getElementById("winning-effect__fish");

      tl.fromTo(
        winningFish,
        1,
        {
          scale: 0,
          opacity: 0,
          visibility: "hidden"
        },
        {
          scale: 1,
          opacity: 1,
          visibility: "visible",
          ease: "elastic.out(1, 0.5)"
        }
      );

      return tl;
    },

    showResult__showText() {
      const tl = gsap.timeline();
      const winningText = document.getElementById("winning-effect__text");

      tl.fromTo(
        winningText,
        1,
        {
          scale: 0,
          opacity: 0,
          visibility: "hidden"
        },
        {
          scale: 1,
          opacity: 1,
          visibility: "visible",
          ease: "elastic.out(1, 0.5)"
        }
      );

      return tl;
    },

    showResult__rotateGlows() {
      const tl = gsap.timeline();
      const verticalGlow = document.getElementById("glow--vertical");
      const circularGlow = document.getElementById("glow--circular");
      const effect = document.getElementById("winning-effect");

      tl.set(effect, {
        width: "100%",
        height: "100%",
        backgroundSize: "cover"
      });

      tl.fromTo(
        verticalGlow,
        2,
        {
          opacity: 0
        },
        {
          opacity: 1,
          visibility: "visible",
          rotate: 540,
          ease: "power4.out"
        },
        0
      );

      tl.fromTo(
        circularGlow,
        2,
        {
          ease: "power4.in",
          opacity: 0
        },
        {
          opacity: 1,
          visibility: "visible",
          rotate: -180,
          ease: "power4.out"
        },
        0
      );

      return tl;
    },

    showResult__showLoseGlow() {
      const tl = gsap.timeline();
      const effect = document.getElementById("winning-effect");
      const glow = document.getElementById("glow--lose");

      tl.set(effect, {
        width: "40%",
        height: "40%",
        backgroundSize: "50% 50%"
      });

      tl.fromTo(
        glow,
        2,
        {
          opacity: 0.7,
          width: "50&"
        },
        {
          opacity: 1,
          width: "100%",
          yoyo: true
        },
        0
      );

      tl.fromTo(
        effect,
        0.5,
        {
          backgroundColor: "rgba(248, 53, 52, 0)",
          boxShadow: "-1px 2px 70px 116px rgba(248, 53, 52, 0)"
        },
        {
          backgroundColor: "rgba(248, 53, 52, 0.4)",
          boxShadow: "-1px 2px 70px 116px rgba(248, 53, 52, 0.4)"
        },
        0
      );

      return tl;
    },

    showResult() {
      const tl = gsap.timeline();

      tl.add(this.showResult__darkenBackground(), 0.1);
      tl.add(this.showResult__showFish(), 0.1);
      tl.add(this.showResult__setEffectVisibility(), 0.3);
      tl.add(this.showResult__showText(), 0.5);
      tl.add(
        this.points === 0
          ? this.showResult__showLoseGlow()
          : this.showResult__rotateGlows(),
        0.5
      );
    },

    showElement(element) {
      const tl = gsap.timeline();

      tl.fromTo(
        element,
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

      return tl;
    },

    setIndicator(scale) {
      const arrow = document.getElementById("indicator__arrow");

      this.gsap.to(this.indicator, 0.15, {
        scale: this.isBaiting ? 0 : scale,
        autoAlpha: 1
      });
      this.gsap.to(arrow, 0.3, {
        y: scale === 0 ? "-=5px" : "+=5px",
        yoyo: true,
        ease: "power4.in",
        repeat: -1
      });
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
    backface-visibility: hidden;
    z-index: 5;
    pointer-events: none;
    animation: floating 0.7s ease-in-out infinite alternate;
    visibility: hidden;
    font-size: 1rem;
    font-weight: bold;
    color: white;

    .indicator__text {
      display: block;
      margin: 0 auto;
      margin-bottom: 5px;
      margin-top: -20px;
    }

    .indicator__arrow {
      display: block;
      margin: 0 auto;
    }

    .indicator__target {
      display: block;
      margin: 0 auto;
    }
  }

  .bait-container {
    position: absolute;
    z-index: 3;
    width: 50px;
    text-align: center;
    visibility: hidden;
    transform: scale(0);

    .bait {
      z-index: 3;
      max-width: 20px;
    }

    .wave {
      z-index: 3;
      height: 0;
      width: 0;
      opacity: 1;
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

    .game-content__title-container {
      position: absolute;
      width: 100%;
      height: 100%;

      .title-1 {
        position: relative;
        margin-left: 10px;
        margin-top: 10px;
        display: block;
        visibility: hidden;
      }

      .title-2 {
        position: relative;
        display: block;
        margin: 0 auto;
      }
    }

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
      background-repeat: no-repeat;
      background-position: bottom;
      visibility: hidden;

      .winning-effect__glow {
        position: absolute;
      }

      .winning-effect__fish {
        z-index: 1;
        visibility: hidden;
        opacity: 0;
      }

      .winning-effect__text {
        font-family: "LuckiestGuy";
        position: relative;
        width: 100%;
        font-size: 1.5rem;
        font-weight: 900;
        visibility: hidden;
        opacity: 0;

        h1 {
          position: absolute;
          left: 50%;
          transform: translateX(-50%);
          padding: 0;
          margin: 0;
        }

        .stroke-inner {
          position: absolute;
          -webkit-text-stroke: 10px #15488d;
          z-index: 1;
        }
        .stroke-outer {
          position: absolute;
          -webkit-text-stroke: 13px #fff;
        }

        .no-stroke {
          color: #71cede;
          position: absolute;
          z-index: 2;
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
