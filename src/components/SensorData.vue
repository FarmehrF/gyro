<template>
  <div>
    <h1>Sensor Data</h1>
    <div>
      <h2>Accelerometer</h2>
      <p>Acceleration X: {{ accelerationX }}</p>
      <p>Acceleration Y: {{ accelerationY }}</p>
      <p>Acceleration Z: {{ accelerationZ }}</p>
    </div>
    <div>
      <h2>Gyroscope</h2>
      <p>Alpha (rotation around z-axis): {{ alpha }}</p>
      <p>Beta (rotation around x-axis): {{ beta }}</p>
      <p>Gamma (rotation around y-axis): {{ gamma }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      accelerationX: 0,
      accelerationY: 0,
      accelerationZ: 0,
      alpha: 0,
      beta: 0,
      gamma: 0,
    };
  },
  methods: {
    handleMotion(event) {
      if (event.acceleration) {
        this.accelerationX = event.acceleration.x;
        this.accelerationY = event.acceleration.y;
        this.accelerationZ = event.acceleration.z;
        console.log('Accelerometer Data:', {
          x: this.accelerationX,
          y: this.accelerationY,
          z: this.accelerationZ,
        });
      }
    },
    handleOrientation(event) {
      this.alpha = event.alpha;
      this.beta = event.beta;
      this.gamma = event.gamma;
      console.log('Gyroscope Data:', {
        alpha: this.alpha,
        beta: this.beta,
        gamma: this.gamma,
      });
    },
  },
  mounted() {
    if (window.DeviceMotionEvent) {
      window.addEventListener('devicemotion', this.handleMotion, true);
    } else {
      console.log('DeviceMotionEvent is not supported');
    }
    if (window.DeviceOrientationEvent) {
      window.addEventListener('deviceorientation', this.handleOrientation, true);
    } else {
      console.log('DeviceOrientationEvent is not supported');
    }
  },
  beforeDestroy() {
    window.removeEventListener('devicemotion', this.handleMotion, true);
    window.removeEventListener('deviceorientation', this.handleOrientation, true);
  },
};
</script>

<style scoped>
h1, h2 {
  font-size: 24px;
}
p {
  font-size: 18px;
}
</style>

