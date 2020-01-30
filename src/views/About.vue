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
  mounted() {
    console.clear();

    const can = document.querySelector(".can > img");

    const randomX = random(10, 20);
    const randomY = random(20, 30);
    const randomTime = random(3, 5);
    const randomTime2 = random(5, 10);
    const randomAngle = random(8, 12);

    gsap.set(can, {
      x: randomX(-1),
      y: randomX(1),
      rotation: randomAngle(-1)
    });

    moveX(can, 1);
    moveY(can, -1);
    rotate(can, 1);

    function rotate(target, direction) {
      gsap.to(target, randomTime2(), {
        rotation: randomAngle(direction),
        // delay: randomDelay(),
        ease: "Sine.easeInOut",
        onComplete: rotate,
        onCompleteParams: [target, direction * -1]
      });
    }

    function moveX(target, direction) {
      gsap.to(target, randomTime(), {
        x: randomX(direction),
        ease: "Sine.easeInOut",
        onComplete: moveX,
        onCompleteParams: [target, direction * -1]
      });
    }

    function moveY(target, direction) {
      gsap.to(target, randomTime(), {
        y: randomY(direction),
        ease: "Sine.easeInOut",
        onComplete: moveY,
        onCompleteParams: [target, direction * -1]
      });
    }

    function random(min, max) {
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
