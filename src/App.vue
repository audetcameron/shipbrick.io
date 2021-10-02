<template>
  <div id="app">
    <p class="foo">
      hello
    </p>
<!--     <a href="https://github.com/whizjs/netlify-identity-demo-vue">
      <img
        style="position: absolute; top: 0; right: 0; border: 0;"
        src="https://s3.amazonaws.com/github/ribbons/forkme_right_red_aa0000.png"
        alt="Fork me on GitHub"
      >
    </a> -->
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <div class="logoWrapper">
      <svg xmlns="http://www.w3.org/2000/svg" version="1.0" width="200pt" height="200pt"
         viewBox="0 0 1259.000000 1280.000000" preserveAspectRatio="xMidYMid meet">
          <metadata>
          </metadata>
          <g transform="translate(0.000000,1280.000000) scale(0.100000,-0.100000)" fill="#000000" stroke="none">
            <path d="M4692 12785 c-117 -33 -219 -119 -274 -233 -31 -63 -33 -73 -33 -177 0 -102 2 -114 31 -175 17 -36 52 -88 77 -116 l47 -52 0 -4428 0 -4429 -2205 -3 -2206 -2 118 -138 c294 -344 529 -598 868 -938 610 -612 1134 -1054 1670 -1409 652 -432 1178 -641 1697 -676 274 -19 728 -4 1093 37 1577 173 3471 893 5410 2057 509 305 1181 752 1544 1026 l55 41 -3755 2 -3754 3 -3 4430 -2 4429 29 31 c119 126 160 289 111 436 -41 120 -113 201 -227 256 -61 29 -87 36 -155 39 -54 3 -101 -1 -136 -11z"/>
            <path d="M5452 11888 c452 -2355 623 -3791 605 -5068 -8 -507 -26 -771 -83 -1160 -90 -620 -263 -1182 -498 -1622 l-72 -135 3126 -6 c1718 -3 3127 -4 3129 -1 12 12 -301 700 -520 1139 -654 1315 -1394 2476 -2338 3665 -804 1013 -1851 2099 -2956 3066 -201 176 -433 374 -438 374 -2 0 18 -114 45 -252z"/>
            <path d="M4220 9345 c0 -29 -82 -368 -126 -524 -277 -971 -745 -1794 -1468 -2580 -353 -384 -693 -695 -1496 -1366 -567 -474 -836 -707 -1030 -893 l-95 -91 2113 -1 2112 0 0 2735 c0 1504 -2 2735 -5 2735 -3 0 -5 -7 -5 -15z"/>
          </g>
      </svg>
    </div>

    <h3>ShipBrick.io</h3>
    <div v-if="isLoggedIn">
       <!-- <Home username={{username}} />-->
       <p>Hello {{ username }} Whats going on?</p>
      <p>
        <button @click="triggerNetlifyIdentityAction('logout')">Log Out</button>
      </p>
    </div>
    <div v-else>
      <p>You are not logged in.</p>
      <p>
        <button @click="triggerNetlifyIdentityAction('login')">Log In</button>
        <button @click="triggerNetlifyIdentityAction('signup')">Sign Up</button>
      </p>
    </div>
    <ul>
      <li>
        <router-link :to="{name:'Home'}">Home Page</router-link>
      </li>
      <li>
        <router-link :to="{name:'Public'}">Public Page</router-link>
      </li>
      <li>
        <router-link :to="{name:'Protected'}">Protected Page</router-link>
      </li>
    </ul>
    <router-view></router-view>


  </div>
</template>

<script>
//import Home from "./components/Home";
//import Public from "./components/Public";
//import Protected from "./components/Protected";

import netlifyIdentity from "netlify-identity-widget";

import { mapGetters, mapActions } from "vuex";

netlifyIdentity.init({
  APIUrl: "https://shipbrick.netlify.app/.netlify/identity",
  logo: true // you can try false and see what happens
});

export default {
  name: "app",
  components: {
  // Home,
   // Public,
   // Protected
  },
  computed: {
    ...mapGetters("user", {
      isLoggedIn: "getUserStatus",
      user: "getUser"
    }),
    username() {
      return this.user ? this.user.username : ", there!";
    }
  },
  data: () => ({
    currentUser: null
  }),
  methods: {
    ...mapActions("user", {
      updateUser: "updateUser"
    }),
    triggerNetlifyIdentityAction(action) {
      if (action == "login" || action == "signup") {
        netlifyIdentity.open(action);
        netlifyIdentity.on(action, user => {
          this.currentUser = {
            username: user.user_metadata.full_name,
            email: user.email,
            access_token: user.token.access_token,
            expires_at: user.token.expires_at,
            refresh_token: user.token.refresh_token,
            token_type: user.token.token_type
          };
          this.updateUser({
            currentUser: this.currentUser
          });
          netlifyIdentity.close();
        });
      } else if (action == "logout") {
        this.currentUser = null;
        this.updateUser({
          currentUser: this.currentUser
        });
        netlifyIdentity.logout();
        this.$router.push({ name: "Home" });
      }
    }
  }
};
</script>

<style lang="scss">
.logoWrapper{
  width:200px;
  height:200px;
  margin:0 auto;
  svg{
    height:200px;
    width:200px;
    display: block;
    margin:0 auto;
  }

}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  .foo{
    color:blue;
  }
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
