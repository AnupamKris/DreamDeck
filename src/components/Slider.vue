<template></template>

<script setup></script>

<style lang="scss" scoped>
.wrapper {
  position: relative;
  height: 20rem;
  width: 3rem;

  &::before,
  &::after {
    display: block;
    position: absolute;
    z-index: 99;
    color: #fff;
    width: 100%;
    text-align: center;
    font-size: 1.5rem;
    line-height: 1;
    padding: 0.75rem 0;
    pointer-events: none;
    font-weight: 200;
  }

  &::before {
    content: "+";
  }

  &::after {
    content: "−";
    bottom: 0;
  }
}

input[type="range"] {
  -webkit-appearance: none;
  background-color: rgba(#fff, 0.2);
  position: absolute;
  top: 50%;
  left: 50%;
  margin: 0;
  padding: 0;
  width: 20rem;
  height: 3.5rem;
  transform: translate(-50%, -50%) rotate(-90deg);
  border-radius: 1rem;
  overflow: hidden;
  cursor: row-resize;

  &[step] {
    background-color: transparent;
    background-image: repeating-linear-gradient(
      to right,
      rgba(#fff, 0.2),
      rgba(#fff, 0.2) calc(12.5% - 1px),
      #05051a 12.5%
    );
  }

  &::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 0;
    box-shadow: -20rem 0 0 20rem rgba(#fff, 0.2);
  }

  &::-moz-range-thumb {
    border: none;
    width: 0;
    box-shadow: -20rem 0 0 20rem rgba(#fff, 0.2);
  }
}
</style>
