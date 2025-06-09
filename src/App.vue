<template>
  <nav>
    <h1>
      <img src="./assets/logo.png" alt="logo" />
      <p>ALRadwan</p>
    </h1>
    <router-link to="/">Home</router-link>
    <router-link to="/cashier">Cashier</router-link>
    <router-link to="/inventory">Inventory</router-link>
    <router-link to="/accounts">Accounts</router-link>
  </nav>
  <router-view />
</template>

<style lang="scss">
* {
  scroll-behavior: smooth;
}

body {
  margin: 0;
}

#app {
  display: flex;
  font-family: 'Times New Roman', Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

  .home,
  .inventory,
  .cashier,
  .accounts {
    min-height: 100vh;
    flex-grow: 1;
    padding-left: 140px;
    background-color: #eef;
  }
}

nav {
  position: fixed;
  font-size: 1.1rem;
  width: 140px;
  height: calc(100vh - 30px);
  background-color: #dfdfdf;
  display: flex;
  flex-flow: column nowrap;
  align-items: start;
  padding: 15px 0px;

  h1 {
    margin: 0 auto;

    img {
      max-width: 80px;
      transform: rotate(180deg);
    }

    p {
      margin: 0;
      position: relative;
      padding-bottom: 10px;
      font-size: 23px;

      &::before {
        position: absolute;
        content: '';
        bottom: 0;
        left: 0;
        width: 100%;
        height: 1px;
        background-color: #42b983;
      }
    }
  }

  a {
    transition: all 0.2s ease-in-out;
    font-weight: bold;
    color: #2c3e50;
    text-decoration: none;
    padding: 10px 20px;
    background-color: #ccccccaa;
    width: 100px;
    text-align: left;

    &:hover {
      color: white;
      background-color: #42b983;
    }

    &.router-link-exact-active {
      color: white;
      background-color: #42b983;
    }
  }
}

button {
  cursor: pointer;
}
</style>

<script>
export default {
  data() {
    return {
      Inventory: [],
    }
  },
}
</script>
