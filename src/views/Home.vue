<template>
  <div class="game">
    <div class="hook" id="hook">CLICK TO CAST</div>

    <div class="game-content">
      <div class="pond" @mouseover="mouseover" @mouseout="mouseout"></div>
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data: () => ({
    hook: null
  }),

  mounted() {
    this.hook = document.getElementById("hook");
    window.addEventListener("mousemove", this.mousemove);
  },

  methods: {
    mouseover() {
      gsap.to(this.hook, 0.15, { scale: 1, autoAlpha: 1 });
    },

    mousemove(e) {
      gsap.to(this.hook, 0.2, {
        css: {
          left: e.pageX + this.hook.clientWidth / 2,
          top: e.pageY + this.hook.clientHeight / 2
        }
      });
    },

    mouseout() {
      gsap.to(this.hook, 0.15, { scale: 0, autoAlpha: 1 });
    }
  }
};
</script>

<style lang="scss">
@import "../assets/keyframes";

$hookWidth: 100px;
$hookHeight: 50px;

.hook {
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
