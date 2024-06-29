<template>
  <div class="container">
    <h1 align="left">Will this work?</h1>
    <div class="p-3 mb-2 bg-secondary" id="demo-div">
      <button class="btn btn-lg btn-success py-1" @click="toggleDemo">{{ isRunning ? 'Stop demo' : 'Start the demo' }}</button>
      <p style="margin-top:1rem;">Num. of datapoints: <span class="badge badge-warning">{{ numObservedEvents }}</span></p>
      <h4 style="margin-top:0.75rem;">Orientation</h4>
      <ul>
        <li>X-axis (&beta;): <span>{{ orientation.beta }}</span><span>&deg;</span></li>
        <li>Y-axis (&gamma;): <span>{{ orientation.gamma }}</span><span>&deg;</span></li>
        <li>Z-axis (&alpha;): <span>{{ orientation.alpha }}</span><span>&deg;</span></li>
      </ul>
      <h4>Accelerometer</h4>
      <ul>
        <li>X-axis: <span>{{ accelerometer.x }}</span><span> m/s<sup>2</sup></span></li>
        <li>Y-axis: <span>{{ accelerometer.y }}</span><span> m/s<sup>2</sup></span></li>
        <li>Z-axis: <span>{{ accelerometer.z }}</span><span> m/s<sup>2</sup></span></li>
        <li>Data Interval: <span>{{ accelerometer.interval }}</span><span> ms</span></li>
      </ul>
      <h4>Accelerometer including gravity</h4>
      <ul>
        <li>X-axis: <span>{{ accelerometerIncludingGravity.x }}</span><span> m/s<sup>2</sup></span></li>
        <li>Y-axis: <span>{{ accelerometerIncludingGravity.y }}</span><span> m/s<sup>2</sup></span></li>
        <li>Z-axis: <span>{{ accelerometerIncludingGravity.z }}</span><span> m/s<sup>2</sup></span></li>
      </ul>
      <h4>Gyroscope</h4>
      <ul>
        <li>X-axis: <span>{{ gyroscope.beta }}</span><span>&deg;/s</span></li>
        <li>Y-axis: <span>{{ gyroscope.gamma }}</span><span>&deg;/s</span></li>
        <li>Z-axis: <span>{{ gyroscope.alpha }}</span><span>&deg;/s</span></li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      numObservedEvents: 0,
      isRunning: false,
      orientation: {
        alpha: 0,
        beta: 0,
        gamma: 0
      },
      accelerometer: {
        x: 0,
        y: 0,
        z: 0,
        interval: 0
      },
      accelerometerIncludingGravity: {
        x: 0,
        y: 0,
        z: 0
      },
      gyroscope: {
        alpha: 0,
        beta: 0,
        gamma: 0
      }
    };
  },
  methods: {
    handleOrientation(event) {
      this.orientation.alpha = event.alpha ? event.alpha.toFixed(2) : 0;
      this.orientation.beta = event.beta ? event.beta.toFixed(2) : 0;
      this.orientation.gamma = event.gamma ? event.gamma.toFixed(2) : 0;
      this.incrementEventCount();
    },
    handleMotion(event) {
      this.accelerometerIncludingGravity.x = event.accelerationIncludingGravity.x ? event.accelerationIncludingGravity.x.toFixed(2) : 0;
      this.accelerometerIncludingGravity.y = event.accelerationIncludingGravity.y ? event.accelerationIncludingGravity.y.toFixed(2) : 0;
      this.accelerometerIncludingGravity.z = event.accelerationIncludingGravity.z ? event.accelerationIncludingGravity.z.toFixed(2) : 0;

      this.accelerometer.x = event.acceleration.x ? event.acceleration.x.toFixed(2) : 0;
      this.accelerometer.y = event.acceleration.y ? event.acceleration.y.toFixed(2) : 0;
      this.accelerometer.z = event.acceleration.z ? event.acceleration.z.toFixed(2) : 0;
      this.accelerometer.interval = event.interval ? event.interval.toFixed(2) : 0;

      this.gyroscope.alpha = event.rotationRate.alpha ? event.rotationRate.alpha.toFixed(2) : 0;
      this.gyroscope.beta = event.rotationRate.beta ? event.rotationRate.beta.toFixed(2) : 0;
      this.gyroscope.gamma = event.rotationRate.gamma ? event.rotationRate.gamma.toFixed(2) : 0;
      this.incrementEventCount();
    },
    incrementEventCount() {
      this.numObservedEvents += 1;
    },
    toggleDemo(e) {
      e.preventDefault();

      // Request permission for iOS 13+ devices
      if (DeviceMotionEvent && typeof DeviceMotionEvent.requestPermission === "function") {
        DeviceMotionEvent.requestPermission().then(response => {
          if (response === 'granted') {
            this.setupEventListeners();
          }
        }).catch(console.error);
      } else {
        this.setupEventListeners();
      }

      if (this.isRunning) {
        window.removeEventListener("devicemotion", this.handleMotion);
        window.removeEventListener("deviceorientation", this.handleOrientation);
        this.isRunning = false;
      } else {
        window.addEventListener("devicemotion", this.handleMotion);
        window.addEventListener("deviceorientation", this.handleOrientation);
        this.isRunning = true;
      }
    },
    setupEventListeners() {
      window.addEventListener("devicemotion", this.handleMotion);
      window.addEventListener("deviceorientation", this.handleOrientation);
    }
  }
};
</script>

<style scoped>
#demo-div {
  color: lightgrey;
  border-radius: 0.3rem;
}
#demo-div span, #demo-div #num-observed-events {
  color: black;
}
h1 {
  margin-top: 0.5rem;
}
h4 {
  margin-top: 0.66rem;
  font-size: 1.33rem;
}
#demo-div li {
  line-height: 21px;
}
#demo-div ul {
  margin-bottom: 0.66rem;
}
</style>