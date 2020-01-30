<template>
  <div>
    <div class="can">
      <img
        src="https://cdn.shopify.com/s/files/1/0223/3113/products/web_size_cans_00001_grande_b45a11b0-902a-412b-8754-c0deef647a5a.png?v=1562456367"
      />
    </div>
  </div>
</template>

<script>
import { gsap } from "gsap";
export default {
  data: () => ({
    randomX: 0,
    randomY: 0,
    randomTime: 0,
    randomTime2: 0,
    randomAngle: 0
  }),

  mounted() {
    const can = document.querySelector(".can > img");

    this.randomX = this.random(10, 20);
    this.randomY = this.random(20, 30);
    this.randomTime = this.random(3, 5);
    this.randomTime2 = this.random(5, 10);
    this.randomAngle = this.random(8, 12);

    gsap.set(can, {
      x: this.randomX(-1),
      y: this.randomX(1),
      rotation: this.randomAngle(-1)
    });

    this.moveX(can, 1);
    this.moveY(can, -1);
    this.rotate(can, 1);
  },

  methods: {
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
.can {
  display: flex;
  height: 100%;
  padding: 5rem;
}
.can > img {
  max-width: 150px;
}
</style>
