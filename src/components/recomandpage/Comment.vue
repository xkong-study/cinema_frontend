<template>
  <div id="poster" v-if="id!==undefined">
    <el-container>
      <el-header height="20px"></el-header>
      <el-container>
        <el-aside width="60%" style="padding-left:50px;padding-right: 30px;">
          <div class="slide-content" align="left">
            <p class="count">{{ id.title }}({{ id.date }})</p>
            <div class="rateAndType">
              <rate
                  :grade=id.rate
                  style="margin-right: 20px"
              >
              </rate>
              {{ id.type }}
            </div>
            <div class="movie-profile">
              <p>director：{{ id.director }}</p>
              <p>editor：{{ id.scriptwriter }}</p>
              <p>actor：{{ id.actors }}</p>
              <p>country/region：{{ id.district }}</p>
              <p>language：{{ id.language }}</p>
              <p>time：{{ id.duration }}</p>
            </div>
          </div>
        </el-aside>
        <el-main>
          <div class="main-slide">
            <div class="top-n slides">
              <img :src="id.cover"/>
            </div>
            <div class="function">
              <div>
                <img src='../../assets/ico/more.png' alt=""/>
                <span class="play" style="cursor:pointer">comments</span>
              </div>
            </div>
          </div>
        </el-main>
      </el-container>
      <form style="width:90%;margin-left: 3%;display:flex">
        <el-input
            type="textarea"
            :autosize="{ minRows: 2, maxRows: 4}"
            placeholder="Comment"
            style="width:80%"
            v-model="textarea">
        </el-input>
        <el-rate
            v-model="rating"
            show-score
            text-color="#ff9900"
            score-template="{value}">
        </el-rate>
        <el-button @click="add()">comment</el-button>
      </form>
      <div style="width:80%;background: rgba(255, 255, 255, 0.2);border-radius: 16px;box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);backdrop-filter: blur(5px);-webkit-backdrop-filter: blur(5px);border: 1px solid rgba(255, 255, 255, 0.3);margin-left: 3%;margin-top: 20px">
        <p v-for="(comment, index) in comments" :key="index" style="text-align: left;margin-left: 1%">
          {{ comment }}
        </p>
      </div>
    </el-container>
  </div>
</template>

<script>
import {VueperSlides, VueperSlide} from 'vueperslides'
import Rate from "@/components/common/Rate"
import 'vueperslides/dist/vueperslides.css'
import '../../assets/my-ele-css/my-loading.css'
import axios from "axios";
import TranslateForm from "./translator"

export default {
  name: "Comment",
  components: {VueperSlide, VueperSlides, Rate,TranslateForm},
  computed: {
    id() {
      return this.$route.params.id
    }
  },
  data() {
    return {
      textarea: '',
      username: JSON.parse(localStorage.getItem("user")).username,
      textToTranslate: "",
      lang: "",
      comments: [],
      rating:3,
    }
  },
  methods: {
    refresh() {
      const loading = this.$loading({
        lock: true,
        text: 'Comments are being generated for you',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 1)'
      });
      setTimeout(() => {
        loading.close();
      }, 2000);
      this.currentPage += 1;
    },
    onCollectClick() {
      if (this.isCollected) {
        this.isCollected = false
      } else {
        this.isCollected = true
      }
    },
    add(){
      this.$http.get('http://route.showapi.com/32-9?showapi_appid=1385352&showapi_sign=eb5c2e7ae797431db5366bfd821a698c&q='+this.textarea+'&fromLanguage=auto&toLanguage=1')
          .then((response)=> {
            let comment_content = response.body.showapi_res_body.translation[0]
            console.log(comment_content,this.rating)
            axios.get('/user/comment/add', {
              params: {
                commentId: this.id.id,
                username: this.username,
                comment:this.textarea,
                comment_zh: comment_content,
                rating:this.rating,
              }
            })
                .then(function (response) {
                  console.log(response);
                })
                .catch(function (error) {
                  console.log(error);
                });
          })
      let key = this.id.id
      setTimeout(()=>{
        axios.get('/user/comment/getall', {})
            .then((response) => { // 使用箭头函数
              this.comments = []
              response.data.map(i => {
                if (i.cinema_id == key) {
                  console.log(i)
                  this.comments.push(i.username + ":" + i.comment + "(" + i.mood + ")")
                }
              })
            })
            .catch(function (error) {
              console.log(error);
            })
      },2000)
    }
    },
  created() {
    const loading = this.$loading({
      lock: true,
      text: 'Comments are being generated for you',
      spinner: 'el-icon-loading',
      background: 'rgba(0, 0, 0, 1)'
    });
    setTimeout(() => {
      loading.close();
    }, 1000);
    this.currentPage += 1;
  },
}
</script>

<style scoped>

p {
  color: #3a91ba;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 6;
  -webkit-box-orient: vertical;
}

div.slide-content p.count {
  font-size: 45px;
  margin-top: 20px;
  margin-bottom: 20px;
}

div.slide-content p {
  font-size: 20px;
}

.function img:hover {
  transform: scale(1.2);
}

.function img {
  vertical-align: middle;
}

.function {
  margin-top: 40px;
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.function span {
  color: #3a91ba;
  padding-right: 70px;
  vertical-align: middle;
  padding-left: 15px;
}

.main-slide {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/*
.arrows {
  margin-top: 30px;
  padding-right: 60px;
}*/
.arrows img:hover {
  transform: scale(1.2);
}

/*
.icon-arrow-right {
  margin-left: 15px;
}

.icon-arrow-left {
  margin-right: 15px;
}
*/
.rateAndType {
  display: flex;
  align-items: center;
}
</style>
