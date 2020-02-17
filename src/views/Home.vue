<template>
  <div class="fishing-game" @mousemove="mousemove">
    <div class="audio-container" style="visibility: visible">
      <audio src="@/assets/sounds/BG_music1.mp3" id="bg_sound" autoplay loop />
      <audio src="@/assets/sounds/rod_pull1.mp3" id="pull_sound" />
      <audio src="@/assets/sounds/rod_throw1.mp3" id="throw_sound" />
      <audio src="@/assets/sounds/water_drop1.mp3" id="drop_sound" />
      <audio src="@/assets/sounds/winning1.mp3" id="winning_sound" />
      <audio src="@/assets/sounds/losing3.mp3" id="losing_sound" />
    </div>
    <div class="indicator" id="indicator" v-if="!isTouch">
      <img src="@/assets/compressed/cast.png" class="indicator__text" alt />
      <img src="@/assets/compressed/arrow.png" class="indicator__arrow" id="indicator__arrow" alt />
      <img src="@/assets/compressed/target.png" class="indicator__target" alt />
    </div>

    <div class="bait-container" id="bait-container">
      <img class="bait" id="bait" src="@/assets/svg/lure.svg" alt />
      <div class="wave" id="wave" />
    </div>

    <div class="game-content">
      <div class="game-content__title-container">
        <img src="@/assets/svg/title-1.svg" id="title-1" class="title-1" alt />
        <img src="@/assets/svg/title-2.svg" id="title-2" class="title-2" alt />
        <img
          :src="require(`@/assets/svg/sound_${hasSound ? 'on' : 'off'}.svg`)"
          id="sound-icon"
          class="sound-icon"
          @click="switchSound"
          alt
        />
      </div>
      <img
        :src="require(`@/assets/compressed/fishing-rod.png`)"
        class="fishing-rod"
        id="fishing-rod"
        alt
      />

      <img
        :src="require(`@/assets/compressed/${isMobile ? 'bg_mob' : 'bg_pc'}.png`)"
        class="game-content__bg"
        id="game-content__bg"
        alt
      />

      <div class="winning-effect" id="winning-effect">
        <img
          src="@/assets/compressed/0_light.png"
          v-if="points === 0"
          class="winning-effect__glow"
          id="glow--lose"
          alt
        />
        <img
          src="@/assets/compressed/glow1.png"
          v-if="points > 0"
          class="winning-effect__glow"
          id="glow--vertical"
          alt
        />
        <img
          src="@/assets/compressed/glow2.png"
          v-if="points > 0"
          class="winning-effect__glow"
          id="glow--circular"
          alt
        />
        <div class="winning-effect__fish-points">
          <img
            v-if="isMobile"
            :src="require(`@/assets/compressed/fishes/fish${points}.png`)"
            class="winning-effect__fish"
            id="winning-effect__fish"
            alt
          />
          <img
            v-else
            :src="require(`@/assets/svg/fishes2/fish${points}.svg`)"
            class="winning-effect__fish"
            id="winning-effect__fish"
            alt
          />
          <br />
          <div class="winning-effect__text" id="winning-effect__text">
            <h1 class="stroke-inner" id="stroke-inner">{{ points * 100 }} P0ints</h1>
            <h1 class="stroke-outer" id="stroke-outer">{{ points * 100 }} P0ints</h1>
            <h1 class="no-stroke" id="no-stroke">{{ points * 100 }} P0ints</h1>
          </div>
        </div>
      </div>

      <div
        class="pond"
        id="pond"
        @mouseover="setIndicator(1)"
        @touchmove="setIndicator(1)"
        @mouseout="setIndicator(0)"
        @click="startBait"
      />
      <div class="pond-mask" id="pond-mask" />
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
    pondMask: null,
    rod: null,
    titleOne: null,
    titleTwo: null,
    soundIcon: null,
    bgSound: null,
    pullSound: null,
    dropSound: null,
    winningSound: null,
    losingSound: null,

    isBaiting: false,
    hasSound: null,

    timeline: null,
    points: 0,

    config: {
      dev: true,
      yoyoGlows: false,
      repeatGlows: false,
      floatDuration: 0.75,
      floatHeight: "-=3px",
      biteLureDepth: "+=10",
      biteLureDuration: 0.5,
      resultBackgroundBrightness: 80,
      glowRotationDuration: 4
    }
  }),

  mounted() {
    gsap.config({
      autoSleep: 60,
      force3D: false,
      nullTargetWarn: false
    });

    this.gsap = gsap;
    this.masterTimeline = gsap.timeline;
    this.initElements();

    this.hasSound = !this.isMobile; // autoplay for mobile browsers is not allowed.
    this.bgSound.volume = 0.5;
    this.bgSound.play();
  },

  computed: {
    isMobile() {
      return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(
        navigator.userAgent
      );
    },

    isTouch() {
      return "ontouchstart" in document.documentElement;
    }
  },

  watch: {
    isBaiting(val) {
      this.gsap.to(this.pondMask, 0.15, {
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
      this.pondMask = document.getElementById("pond-mask");
      this.rod = document.getElementById("fishing-rod");
      this.wave = document.getElementById("wave");
      this.titleOne = document.getElementById("title-1");
      this.titleTwo = document.getElementById("title-2");
      this.soundIcon = document.getElementById("sound-icon");
      this.baitContainer = document.getElementById("bait-container");
      this.bgSound = document.getElementById("bg_sound");
      this.pullSound = document.getElementById("pull_sound");
      this.throwSound = document.getElementById("throw_sound");
      this.dropSound = document.getElementById("drop_sound");
      this.winningSound = document.getElementById("winning_sound");
      this.losingSound = document.getElementById("losing_sound");
    },

    resetElements() {
      this.gsap.set([this.baitContainer, this.lure, this.wave], {
        clearProps: "all"
      });
    },

    switchSound() {
      this.hasSound = !this.hasSound;

      this.hasSound ? this.bgSound.play() : this.bgSound.pause();
      const volume = this.hasSound ? 1 : 0;

      this.bgSound.volume = this.hasSound ? 0.5 : 0;
      this.pullSound.volume = volume;
      this.dropSound.volume = volume;
      this.winningSound.volume = volume;
    },

    moveRod(e, distance = 100) {
      const gameContent = document.getElementsByClassName("game-content")[0];
      const gameContentX = gameContent.getBoundingClientRect().left;

      this.gsap.to(this.rod, 0.3, {
        left: e.pageX - (gameContentX + window.scrollX) + distance
      });
    },

    startBait_animateRod() {
      const tl = gsap.timeline();
      const vm = this;

      vm.throwSound.volume = 0.5;
      setTimeout(() => {
        vm.throwSound.play();
      }, 400);

      tl.to(
        this.rod,
        1,
        {
          x: "+=200",
          rotate: 40,
          ease: "power4.out",
          onStart: () => {
            if (vm.isMobile) {
              vm.gsap.to(vm.rod, 1.5, {
                visibility: "visible"
              });
            }
          }
        },
        0
      );

      tl.to(
        this.rod,
        1,
        {
          x: "-=100",
          rotate: 0,
          ease: "elastic.out(1, 0.4)"
        },
        0.5
      );

      return tl;
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
      const vm = this;

      tl.fromTo(
        this.baitContainer,
        1.3,
        {
          visibility: 0,
          scale: 0,
          top: e.pageY - 200,
          left: e.pageX - 25,
          onComplete: () => {
            setTimeout(() => {
              vm.dropSound.play();
            }, 1300);
          }
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
        [this.titleOne, this.soundIcon],
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

    // Decide fish to get
    startBait(e) {
      if (this.isBaiting) return;

      this.points = Math.floor(Math.random() * 5);
      this.setWinningEffect(this.points);

      if (this.isMobile) {
        this.moveRod(e, 10);
      }

      let tl = gsap.timeline();

      this.isBaiting = true;

      tl.add(this.startBait__switchTitle(), 0);
      tl.add(this.startBait_animateRod(), 0.2);
      tl.add(this.startBait__throwLure(e), 0.5);
      tl.add(this.startBait__animateWave(), 1.3);

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

      tl.to(
        this.baitContainer,
        this.config.biteLureDuration,
        {
          ease: "power4.out",
          y: this.config.biteLureDepth
        },
        0
      );
      tl.to(
        this.rod,
        0.8,
        {
          rotate: 40,
          ease: "back.in(1.7)",
          opacity: 0
        },
        0
      );

      return tl;
    },

    catchFish__getLure() {
      const tl = gsap.timeline();

      this.pullSound.play();

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
      tl.add(this.catchFish__getLure(), 0.5);
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

      tl.to(effect, 0.5, { visibility: "visible" });

      return tl;
    },

    showResult__showFish() {
      const tl = gsap.timeline();
      const winningFish = document.getElementById("winning-effect__fish");
      const scale = this.isMobile
        ? `1.1${this.points}`
        : `1.${this.points + 2}`;

      tl.to(winningFish, 1, {
        visibility: "visible",
        opacity: 1,
        scale: Number(scale),
        ease: "elastic.out(1, 0.5)"
      });

      return tl;
    },

    showResult__showText() {
      const tl = gsap.timeline();
      const winningText = document.getElementById("winning-effect__text");

      tl.to(winningText, 1, {
        visibility: "visible",
        scale: 1,
        opacity: 1,
        ease: "elastic.out(1, 0.5)"
      });

      return tl;
    },

    showResult__rotateGlows() {
      const tl = gsap.timeline({
        repeat: this.config.repeatGlows ? -1 : 0,
        yoyo: this.config.yoyoGlows
      });
      const verticalGlow = document.getElementById("glow--vertical");
      const circularGlow = document.getElementById("glow--circular");

      tl.to(
        verticalGlow,
        this.config.glowRotationDuration,
        {
          opacity: 1,
          visibility: "visible",
          rotate: 540,
          ease: "power2.inOut"
        },
        0
      );

      tl.to(
        circularGlow,
        this.config.glowRotationDuration,
        {
          opacity: 1,
          visibility: "visible",
          rotate: -180,
          ease: "power2.inOut"
        },
        0
      );

      return tl;
    },

    showResult__showLoseGlow() {
      const tl = gsap.timeline();
      const effect = document.getElementById("winning-effect");
      const glow = document.getElementById("glow--lose");

      tl.fromTo(
        glow,
        2,
        {
          visibility: "hidden",
          opacity: 0,
          width: "50&"
        },
        {
          visibility: "visible",
          opacity: 1,
          width: "150%",
          yoyo: true
        },
        0
      );

      tl.fromTo(
        effect,
        0.5,
        {
          backgroundColor: "rgba(0, 0, 0, 0)",
          boxShadow: "-1px 2px 70px 116px rgba(0, 0, 0, 0)"
        },
        {
          backgroundSize: "contain",
          borderRadius: "50%",
          backgroundColor: "rgba(248, 53, 52, 0.2)",
          boxShadow: "-1px 2px 50px 96px rgba(248, 53, 52, 0.2)"
        },
        0
      );

      return tl;
    },

    showResult() {
      const repeat = this.config.dev === true ? -1 : 0;
      const yoyo = this.config.dev === true;
      const tl = gsap.timeline({ repeat, yoyo });
      const isWin = this.points > 0;

      this.bgSound.volume = isWin ? "0.05" : "0";

      isWin ? this.winningSound.play() : this.losingSound.play();

      tl.add(this.showResult__setEffectVisibility(), 0);

      if (isWin) {
        tl.add(
          isWin
            ? this.showResult__rotateGlows()
            : this.showResult__showLoseGlow(),
          0
        );
      } else {
        tl.add(this.showResult__showLoseGlow(), 0);
      }

      tl.add(this.showResult__darkenBackground(), 0.1);
      tl.add(this.showResult__showFish(), 0.1);
      tl.add(this.showResult__showText(), 0.3);
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
      if (this.isTouch) return;
      const arrow = document.getElementById("indicator__arrow");

      this.gsap.to(this.indicator, 0.15, {
        scale: this.isBaiting ? 0 : scale,
        autoAlpha: 1
      });

      if (this.isBaiting) return;

      this.gsap.to(arrow, 0.3, {
        y: scale === 0 ? "-=5px" : "+=5px",
        yoyo: true,
        ease: "power4.in",
        repeat: -1
      });
    },

    mousemove(e) {
      if (this.isTouch || this.isBaiting) return;

      this.gsap.to(this.indicator, 0.2, {
        left: e.pageX + this.indicator.clientWidth / 2,
        top: e.pageY + this.indicator.clientHeight / 2
      });

      // This is about getting the parent's offset
      this.moveRod(e);
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/main";
</style>
