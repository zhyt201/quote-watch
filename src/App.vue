<template>
  <div id="app">
    <header>
      <span>Quote Watch</span>
      <autocomplete></autocomplete>
      <button aria-label="Refresh"></button>
    </header>
    <main>
    </main>
  </div>
</template>

<script>
import Autocomplete from './components/Autocomplete'

export default {
  name: 'app',
  components: {
    Autocomplete
  }
}
</script>

<style>
* {
    box-sizing: border-box;
}

html,
body {
    padding: 0;
    margin: 0;
    height: 100%;
    width: 100%;
    font-family: 'Helvetica', 'Verdana', sans-serif;
    font-weight: 400;
    color: #444;
}

html {
    overflow: hidden;
}

body {
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    justify-content: flex-start;
    align-items: stretch;
    align-content: stretch;
    background: #ececec;
}

header {
  position: relative;
  width: 100%;
  height: 56px;
  color: #FFF;
  background: #ff0000;
  position: fixed;
  font-size: 20px;
  box-shadow: 0 4px 5px 0 rgba(0, 0, 0, 0.14), 0 2px 9px 1px rgba(0, 0, 0, 0.12), 0 4px 2px -2px rgba(0, 0, 0, 0.2);
  padding: 16px 16px 0 16px;
  display: flex;
  z-index: 1000;
}

header span {
    font-weight: 400;
    font-size: 20px;
    margin: 0;
    flex: 1;
}

header button {
    width: 24px;
    height: 24px;
    margin-right: 16px;
    text-indent: -30000px;
    overflow: hidden;
    opacity: 0.8;
    transition: opacity 0.333s cubic-bezier(0, 0, 0.21, 1);
    border: none;
    outline: none;
    cursor: pointer;
     background: url(assets/ic_refresh_white_24px.svg) center center no-repeat;
}

</style>
