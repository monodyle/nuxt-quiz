<template>
  <div>
    <nuxt />
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css?family=Montserrat:400,500,600,700&display=swap&subset=vietnamese');
* {
  box-sizing: border-box;
}
body {
  background-color: #f6f9f8;
}
html {
  font-family: 'Montserrat', 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
}
.page-enter-active,
.page-leave-active,
.layout-enter-active,
.layout-leave-active {
  transition: opacity .5s
}
.page-enter,
.page-leave-active,
.layout-enter,
.layout-leave-active {
  opacity: 0
}
</style>
