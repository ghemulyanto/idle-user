<template>
  <div id="app">
    <nav>
      <div>
        <router-link to="/">Home</router-link>
      </div>
      <div>
        <router-link to="/login">Login</router-link>
      </div>
      <div>
        <router-link to="/about">About</router-link>
      </div>
    </nav>
    <router-view />
    User is inactive : {{ isInactive }}
  </div>
</template>

<script>
import {
  INACTIVE_USER_TIME_THRESHOLD,
  USER_ACTIVITY_THROTTLER_TIME
} from "@/constant";
export default {
  name: "App",
  data: () => ({
    isInactive: false,
    userActivityThrottlerTimeout: null,
    userActivityTimeout: null
  }),
  methods: {
    activateActivitiyTracker() {
      window.addEventListener("mousemove", this.userActivityThrottler);
      window.addEventListener("scroll", this.userActivityThrottler);
      window.addEventListener("keydown", this.userActivityThrottler);
      window.addEventListener("resize", this.userActivityThrottler);
    },
    deactivateActivityTracker() {
      window.removeEventListener("mousemove", this.userActivityThrottler);
      window.removeEventListener("scroll", this.userActivityThrottler);
      window.removeEventListener("keydown", this.userActivityThrottler);
      window.removeEventListener("resize", this.userActivityThrottler);
    },
    resetUserActivityTimeout() {
      clearTimeout(this.userActivityTimeout);
      this.userActivityTimeout = setTimeout(() => {
        this.userActivityThrottler();
        this.inactiveUserAction();
      }, INACTIVE_USER_TIME_THRESHOLD);
    },
    userActivityThrottler() {
      if (this.isInactive) {
        this.isInactive = false;
      }
      if (!this.userActivityThrottlerTimeout) {
        this.userActivityThrottlerTimeout = setTimeout(() => {
          this.resetUserActivityTimeout();
          clearTimeout(this.userActivityThrottlerTimeout);
          this.userActivityThrottlerTimeout = null;
        }, USER_ACTIVITY_THROTTLER_TIME);
      }
    },
    inactiveUserAction() {
      this.isInactive = true;
      this.$router.push({ name: "login" });
    }
  },
  beforeMount() {
    this.activateActivitiyTracker();
  },
  beforeDestroy() {
    this.deactivateActivityTracker();
    clearTimeout(this.userActivityTimeout);
    clearTimeout(this.userActivityThrottlerTimeout);
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
