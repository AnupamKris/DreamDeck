<script setup>
import { io } from "socket.io-client";
import { onMounted, ref } from "vue";
import { StatusBar, Style } from "@capacitor/status-bar";

const socket = ref(null);
const ip = ref("192.168.199.146");
// const monitors = ref([]);

const brightness = ref({});
const volume = ref({});
const currentMode = ref("");

// 192.168.199.19
const connectSocket = () => {
  socket.value = io("http://" + ip.value + ":8899");
  hideStatusBar();
  socket.value.on("connect", () => {
    console.log("connected");
  });
  socket.value.on("displays", (data) => {
    brightness.value = data;
    console.log("DISPLAYS", data);
  });
  socket.value.on("volumes", (data) => {
    volume.value = data;
    console.log("VOLUMES", data);
  });
  currentMode.value = "menu";
};

const changeBrightness = (m) => {
  console.log(brightness.value);
  socket.value.emit("brightness", { value: brightness.value[m], mon: m });
};

const changeVolume = (m) => {
  console.log(volume.value);
  socket.value.emit("volume", { value: volume.value["master"] });
};

const setVolume = (m) => {
  if (m == "low") {
    volume.value["master"] = "0.25";
  } else if (m == "medium") {
    volume.value["master"] = "0.50";
  } else if (m == "high") {
    volume.value["master"] = "1.00";
  } else if (m == "mute") {
    volume.value["master"] = "0.0";
  }
  socket.value.emit("volume", { value: volume.value["master"] });
};

const setProcessVolume = (p) => {
  socket.value.emit("processVolume", { value: volume.value[p], process: p });
};

const refreshVolume = () => {
  socket.value.emit("refreshVolume");
};

const launchApp = (app) => {
  socket.value.emit("launchApp", { app: app });
};

const hideStatusBar = async () => {
  await StatusBar.hide();
};

onMounted(() => {
  hideStatusBar();
});
</script>

<template>
  <main>
    <div class="init" v-if="!socket">
      <input type="text" placeholder="ip address" v-model="ip" />
      <button @click="connectSocket">Connect</button>
    </div>
    <button
      class="back"
      @click="currentMode = 'menu'"
      v-if="currentMode != 'menu'"
    >
      <ion-icon name="chevron-back-circle-outline"></ion-icon>
    </button>
    <button
      class="refresh"
      @click="refreshVolume"
      v-if="currentMode == 'volume'"
    >
      <ion-icon name="refresh-outline"></ion-icon>
    </button>
    <div class="display-controls" v-if="currentMode == 'display'">
      <div class="brightness-control" v-for="(value, m) in brightness">
        <div class="wrapper">
          <input
            type="range"
            min="0"
            max="100"
            orient="vertical"
            v-model="brightness[m]"
            @change="changeBrightness(m)"
          />
        </div>
        <label for="">{{ m }}</label>
      </div>
    </div>
    <div class="display-controls" v-if="currentMode == 'volume'">
      <div class="brightness-control">
        <div class="wrapper">
          <input
            type="range"
            min="0"
            max="1"
            orient="vertical"
            v-model="volume['master']"
            @input="changeVolume('master')"
            step="0.01"
          />
        </div>
        <label for=""><ion-icon name="volume-high-outline"></ion-icon></label>
      </div>
      <div class="brightness-control around">
        <button @click="setVolume('high')">
          <ion-icon name="volume-high-outline"></ion-icon>
        </button>
        <button @click="setVolume('medium')">
          <ion-icon name="volume-medium-outline"></ion-icon>
        </button>
        <button @click="setVolume('low')">
          <ion-icon name="volume-low-outline"></ion-icon>
        </button>
        <button @click="setVolume('mute')">
          <ion-icon name="volume-mute-outline"></ion-icon>
        </button>
      </div>
      <div class="brightness-control" v-for="(value, m) in volume" :class="m">
        <div class="wrapper">
          <input
            type="range"
            min="0"
            max="1"
            orient="vertical"
            v-model="volume[m]"
            @change="setProcessVolume(m)"
            step="0.01"
          />
        </div>
        <label for="">{{ m }}</label>
      </div>
    </div>
    <div class="menu" v-if="currentMode == 'menu'">
      <button @click="currentMode = 'display'">
        <ion-icon name="sunny-outline"></ion-icon>
      </button>
      <button @click="currentMode = 'volume'">
        <ion-icon name="volume-medium-outline"></ion-icon>
      </button>
      <button @click="launchApp('discord')">
        <ion-icon name="logo-discord"></ion-icon>
      </button>
      <button>..</button>
      <button>..</button>

      <button>..</button>
      <button>..</button>
      <button>..</button>
      <button>..</button>
      <button>..</button>

      <button>..</button>
      <button>..</button>
      <button>..</button>
      <button>..</button>
      <button><ion-icon name="cog-outline"></ion-icon></button>
    </div>
  </main>
</template>

<style lang="scss">
main {
  height: 100%;
  width: 100%;
}
.display-controls {
  height: 100%;
  width: 100%;

  display: flex;
  flex-direction: row;
  // justify-content: center;
  align-items: center;
  // height: 100vh;
  // width: 100vw;
  background-color: #000;
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  text-align: center;

  padding: 0 20px;
  padding-left: 70px;

  .brightness-control {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100px;

    label {
      margin-top: 1rem;
      font-size: 12px;

      ion-icon {
        font-size: 24px;
      }
    }

    button {
      height: 70px;
      width: 70px;

      border-radius: 15px;

      display: flex;
      justify-content: center;
      align-items: center;

      outline: none;
      border: none;
      background-color: #222222;
      color: #fff;
      font-size: 24px;
    }
  }

  .around {
    justify-content: space-around;
    padding: 15px 0;
  }

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
}

.master {
  display: none !important;
}

.init {
  height: 100%;
  width: 100%;

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: #141414;

  input {
    height: 50px;
    width: 300px;
    border-radius: 10px;
    border: 1px solid #222222;
    background: #3a3a3a;
    color: #ffffff;
    outline: none;
    padding: 0 20px;
    font-size: 24px;
    font-weight: 400;
    text-align: center;
    margin-bottom: 10px;
  }

  button {
    height: 50px;
    width: 300px;
    border-radius: 10px;
    border: none;
    outline: none;
    background-color: #222222;
    color: #fff;
    font-size: 24px;
    font-weight: bold;
    text-align: center;
  }
}

.menu {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  height: 100%;
  width: 100%;

  background: #141414;

  button {
    width: calc(20% - 25px);
    height: 27%;
    background-color: #222222;
    color: #fff;
    font-size: 36px;
    font-weight: bold;
    text-align: center;
    border: none;
    border-radius: 10px;

    display: flex;
    justify-content: center;
    align-items: center;
  }
}

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

.refresh {
  position: absolute;
  top: 80px;
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
</style>
