<template>
  <div class="movie-view">
    <div class="hot-title">
      <el-carousel height="500px">
        <el-carousel-item
          v-for="(slide,i) in slides"
          :key="i"
          >
          <img :src="slide.image" style="width: 100%;height: 100%" @click="getImage()"/>
        </el-carousel-item>
      </el-carousel>
    </div>
  </div>
</template>

<script>

import Footer from "@/components/common/Footer";
export default {
  name: "Movie",
  components: {Footer},
  data: () => ({

    slides: [
      {
        image: require('@/assets/movieImages/000000.jpg'),
        url:'/videoplay'
      },
      { image: require('@/assets/movieImages/000001.jpg') },
      { image: require('@/assets/movieImages/000002.jpg') },
      { image: require('@/assets/movieImages/000003.jpg') },
      { image: require('@/assets/movieImages/000004.jpg') },
      { image: require('@/assets/movieImages/000005.jpg') },
      { image: require('@/assets/movieImages/000005.jpg') },
      { image: require('@/assets/movieImages/000007.jpg') },
      { image: require('@/assets/movieImages/000008.jpg') },
      { image: require('@/assets/movieImages/000009.jpg') },
      { image: require('@/assets/movieImages/000010.jpg') },
      { image: require('@/assets/movieImages/000011.jpg') }
    ]
  }),
  methods:{
    getImage(params){
      console.log(params)
      window.location.href="http://localhost:8080/movieset"
    }
  }
}

</script>

<style scoped>
  /deep/ .el-divider__text {
    color: white;
    font-size: 35px;
    background: #19242f;
  }

  div.carousel {
    margin-top: 80px;
  }

  .el-carousel {
    margin-top: 10px;
  }

  div.content-tag {
    align:"left"
  }

  div.content-tag span {
    padding: 0px 50px;
    display: inline-block;
    font-size: 35px;
    color: #3a91ba;
    margin-bottom: 30px;
  }

  div.content-tag hr {
    display: inline-block;
    width: 30%;
    color: #3a91ba;
  }

  /deep/ li.el-carousel__indicator.el-carousel__indicator--horizontal {
    display: none;
  }

</style>
