<template>
  <div class="container">
    <h1 align="left">Will this work?</h1>
    <div class="p-3 mb-2 bg-secondary" id="demo-div">
      <a id="start_demo" class="btn btn-lg btn-success py-1" href="#" role="button" @click="toggleDemo">Start the demo</a>
      <p style="margin-top:1rem;">Num. of datapoints: <span class="badge badge-warning" id="num-observed-events">{{ numObservedEvents }}</span></p>
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
      this.updateFieldIfNotNull('orientation.alpha', event.alpha);
      this.updateFieldIfNotNull('orientation.beta', event.beta);
      this.updateFieldIfNotNull('orientation.gamma', event.gamma);
      this.incrementEventCount();
    },
    handleMotion(event) {
      this.updateFieldIfNotNull('accelerometerIncludingGravity.x', event.accelerationIncludingGravity.x);
      this.updateFieldIfNotNull('accelerometerIncludingGravity.y', event.accelerationIncludingGravity.y);
      this.updateFieldIfNotNull('accelerometerIncludingGravity.z', event.accelerationIncludingGravity.z);

      this.updateFieldIfNotNull('accelerometer.x', event.acceleration.x);
      this.updateFieldIfNotNull('accelerometer.y', event.acceleration.y);
      this.updateFieldIfNotNull('accelerometer.z', event.acceleration.z);

      this.updateFieldIfNotNull('accelerometer.interval', event.interval, 2);

      this.updateFieldIfNotNull('gyroscope.alpha', event.rotationRate.alpha);
      this.updateFieldIfNotNull('gyroscope.beta', event.rotationRate.beta);
      this.updateFieldIfNotNull('gyroscope.gamma', event.rotationRate.gamma);
      this.incrementEventCount();
    },
    updateFieldIfNotNull(fieldName, value, precision = 10) {
      if (value != null) {
        this[fieldName] = value.toFixed(precision);
      }
    },
    incrementEventCount() {
      this.numObservedEvents += 1;
    },
    toggleDemo(e) {
      e.preventDefault();

      // Request permission for iOS 13+ devices
      if (DeviceMotionEvent && typeof DeviceMotionEvent.requestPermission === "function") {
        DeviceMotionEvent.requestPermission();
      }

      if (this.isRunning) {
        window.removeEventListener("devicemotion", this.handleMotion);
        window.removeEventListener("deviceorientation", this.handleOrientation);
        e.target.innerHTML = "Start demo";
        e.target.classList.add('btn-success');
        e.target.classList.remove('btn-danger');
        this.isRunning = false;
      } else {
        window.addEventListener("devicemotion", this.handleMotion);
        window.addEventListener("deviceorientation", this.handleOrientation);
        e.target.innerHTML = "Stop demo";
        e.target.classList.remove('btn-success');
        e.target.classList.add('btn-danger');
        this.isRunning = true;
      }
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