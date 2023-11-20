<template>
  <div class="settings">
    <div class="back" @click="closeSettings">
      <ion-icon name="chevron-back-circle-outline"></ion-icon>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(["close"]);

const closeSettings = () => {
  console.log("clongs");
  emit("close");
};
</script>

<style lang="scss" scoped>
.settings {
  height: 100vh;
  width: 100vw;

  position: fixed;

  top: 0;
  left: 0;

  background: #141414e0;
  backdrop-filter: blur(5px);
  z-index: 5;

  .back {
    position: absolute;
    top: 20px;
    left: 20px;
    background-color: #222;
    color: #fff;
    font-size: 16px;
    font-weight: bold;
    text-align: center;
    border: none;
    border-radius: 10px;
    padding: 10px;
    font-size: 24px;

    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
