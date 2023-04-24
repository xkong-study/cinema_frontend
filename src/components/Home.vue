<template>
    <div class="home">
      <div class="home-view">
        <video-play></video-play>
        <page-content></page-content>
        <router-view/>
      </div>
    </div>
</template>

<style scoped>
  .home-view{
    /*position: relative;*/
    width: 100%;
    height: 100%;
  }
</style>
