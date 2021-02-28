<script>
import Vue from 'vue';
import Login from './pages/login.vue';
import Home from './pages/home.vue';

const routes = {
  '/': Login,
  '/home': Home,
};
const token = sessionStorage.getItem('token') != null
  ? sessionStorage.getItem('token')
  : localStorage.getItem('token');
new Vue({
  el: '#app',
  data: {
    currentRoute: token != null ? window.location.pathname : '/',
  },
  computed: {
    ViewComponent() {
      return routes[this.currentRoute];
    },
  },
  render(h) {
    return h(this.ViewComponent);
  },
});
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
