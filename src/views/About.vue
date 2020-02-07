<template>
  <div>
    <div class="container">
      <h1 class="stroke-inner">100 Points</h1>
      <h1 class="stroke-outer">100 Points</h1>
      <h1 class="no-stroke">100 Points</h1>
    </div>
  </div>
</template>

<script>
export default {
  name: "name",

  data() {
    return {};
  },

  async created() {},

  methods: {}
};
</script>

<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css?family=Luckiest+Guy&display=swap");

.container {
  width: 300px;
  font-size: 5rem;
  font-family: "Luckiest Guy", cursive;

  .stroke-inner {
    position: absolute;
    -webkit-text-stroke: 23px #15488d;
    z-index: 1;
  }
  .stroke-outer {
    position: absolute;
    -webkit-text-stroke: 27px #fff;
  }

  .no-stroke {
    color: #71cede;
    position: absolute;
    z-index: 2;
  }
}
</style>
