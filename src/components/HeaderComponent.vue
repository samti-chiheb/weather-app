<template>
  <header>
    <div class="icons">
      <i class="fa-solid fa-signal"></i>
      <i class="fa-solid fa-wifi"></i>
    </div>
    <div class="indicators">
      <div class="battery">
        <div class="battery-level-container">
          <div
            class="battery-level"
            :style="{
              width: batteryLevel + '%',
              backgroundColor: 'rgb(' + colorRed + ', ' + colorGreen + ', 0)',
            }"
          >
            <i v-if="batteryCharging" class="fa-solid fa-bolt"></i>
          </div>
        </div>
        <i class="fa-solid fa-battery-empty"></i>
      </div>
      <p class="time">{{ hours }} : {{ minutes }}</p>
    </div>
  </header>
</template>

<script>
export default {
  name: "HeaderComponent",
  data() {
    return {
      hours: 0,
      minutes: 0,
      batteryLevel: 100,
      lowBattery: 0,
      batteryCharging: false,
      colorRed: 0,
      colorGreen: 0,
    };
  },
  methods: {
    updateTime() {
      this.hours = new Date().getHours();
      this.minutes =
        new Date().getMinutes() < 10
          ? "0" + new Date().getMinutes()
          : new Date().getMinutes();
    },
    batteryManager() {
      navigator.getBattery().then((battery) => {
        this.batteryLevel = battery.level * 100;
        battery.charging
          ? (this.batteryCharging = true)
          : (this.batteryCharging = false);
      });
    },
    batteryColorManager() {
      this.lowBattery = 100 - this.batteryLevel;
      this.colorRed = this.colorRange(this.lowBattery, 0, 50, 0, 255);
      this.colorGreen = this.colorRange(this.batteryLevel, 0, 100, 100, 255);
    },
    colorRange(value, fromLow, fromHigh, toLow, toHigh) {
      return (
        ((value - fromLow) * (toHigh - toLow)) / (fromHigh - fromLow) + toLow
      );
    },
  },
  beforeMount() {
    this.batteryManager();
    this.updateTime();
    this.batteryColorManager();
    setInterval(() => {
      this.batteryManager();
      this.updateTime();
    }, 500);
  },
};
</script>

<style>
header {
  position:sticky;
  top : 0;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #292828;
  color: #fff;
  border-radius: 4px 4px 0 0;
}

.indicators {
  display: flex;
  gap: 15px;
  margin: 10px;
  align-items: center;
}

.indicators p {
  margin: 0;
  font-size: 20px;
}

.icons {
  display: flex;
  align-items: center;
  gap: 15px;
  margin-left: 10px;
  font-size: 20px;
}

.battery {
  position: relative;
  width: 30px;
  font-size: 30px;
}

.battery-level-container {
  position: absolute;
  top: 10px;
  left: 3px;
  height: 13px;
  width: 86%;
}

.battery-level {
  height: 100%;
}

.battery-level i {
  display: block;
  padding: 2px;
  color: #fff;
  font-size: 9px;
  margin: 0;
}

.battery i {
  position: relative;
  color: #afaaaa;
}
</style>
