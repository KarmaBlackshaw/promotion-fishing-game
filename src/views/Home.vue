<template>
  <div class="game">
    <div class="game--content">
      <div class="pond"></div>
      <div class="fishing-rod">
        <div class="hook" id="hook"></div>
      </div>
    </div>
    <div class="game--action-bar">
      <button class="btn-start" @click="throwHook" :disabled="isTweening">Start</button>
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
import { RoughEase } from "gsap/EasePack";

gsap.registerPlugin(RoughEase);

export default {
  name: "fishing",

  data: () => ({
    x: 0,
    y: 0,
    counter: 0
  }),

  async mounted() {
    gsap.config({
      force3D: false,
      nullTargetWarn: false
    });
  },

  computed: {
    isTweening() {
      return gsap.isTweening("#hook");
    },

    pondProperties() {
      return document.getElementsByClassName("pond")[0];
    }
  },

  methods: {
    throwHook() {
      if (this.isTweening) return;
      this.reset();

      let vue = this;

      gsap.to("#hook", {
        duration: 2.5,
        ease: "expo",
        x: (Math.random() - 0.5) * (this.pondProperties.clientWidth - 20),
        y: -250,
        onComplete() {
          vue.vibrateHook(this);
          vue.counter++;
        }
      });
    },

    vibrateHook(vm) {
      let counter = 0;
      let interval = setInterval(() => {
        gsap.fromTo(
          vm._targets,
          0.15,
          {
            x: Math.random() * 5 + vm.vars.x,
            y: Math.random() * 5 + vm.vars.y
          },
          {
            x: Math.random() * 5 + vm.vars.x,
            y: Math.random() * 5 + vm.vars.y,
            repeat: 5,
            yoyo: true,
            ease: "power1"
          }
        );

        if (++counter === 3) {
          clearInterval(interval);
          this.reset();

          setTimeout(() => {
            this.getFish();
          }, 1000);
        }
      }, 2000);
    },

    reset() {
      gsap.to("#hook", { duration: 2, x: 0, y: 0 });
      gsap.set("#hook", {
        duration: 2,
        x: 0,
        y: 0
      });
    },

    getFish: () => {
      console.log(`Your fish is worth ${Math.floor(Math.random() * 100)}`);
    }
  }
};
</script>

<style lang="scss" scoped>
// ratio 1:1.75

.game--content {
  display: flex;
  justify-content: center;
  position: relative;

  .pond {
    height: 400px;
    width: 700px;
    background: #0c2461;
  }

  .fishing-rod {
    position: absolute;
    width: 5px;
    background: red;
    height: 30px;
    bottom: 0;
  }

  .hook {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    bottom: 30px;
    height: 10px;
    width: 10px;
    border-radius: 10px;
    background: yellow;
  }
}

.game--action-bar {
  .btn-start {
    width: 100%;
    margin-top: 10px;
  }
}
</style>
