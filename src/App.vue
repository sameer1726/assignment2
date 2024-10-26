<template>
  <div id="app">
    <!-- <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </nav> -->
    <router-view />
  </div>
</template>

<script>
export default {
  data() {
    return {
      backgroundImage: "",
    };
  },
  methods: {
    change_bg(response) {
      response.current.condition.text;
    },
  },
  created() {
    this.$root.$on("change_bg", this.change_bg);
  },
  destroyed() {
    this.$root.$off("change_bg", this.change_bg);
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;

  background-image: url("@/assets/images/cloudy-bg.jpg");
  background-size: cover;
  background-position: center;
  width: 100vw;
  height: 100vh;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
