<template>
  <div class="movie-nav" style="margin-top: 20px">
    <div class="chooseMovie" style="display: flex">
      <el-tag effect="dark" style="margin-left: 10px">select movie</el-tag>
    </div>
    <div class="chooseBytype" style="display: flex;width: 100%;flex-wrap: wrap;margin-top: 10px">
      <el-radio-group
          v-model="cid"
          @change="handleSelect"
      >
        <el-radio v-for="(val,key) in types" :key="key" :label="key">{{val}}</el-radio>
      </el-radio-group>
    </div>
    <el-divider></el-divider>
    <div class="chooseByOther" style="margin-left: 15px">
      <el-radio-group
        v-model="dateOrRate"
        @change="handleSelect2"
      >
        <el-radio label="1">sort by score</el-radio>
        <el-radio label="2">sort by time</el-radio>
      </el-radio-group>
    </div>
  </div>
</template>

<script>
const categories = {
  0: 'all', 1: 'plot', 2: 'comedy', 3: 'action',
  4: 'love', 5: 'sci-fi', 6: 'cartoon', 7: 'suspenseful',
  8: 'thriller', 9: 'fear', 10: 'crime', 11: 'homosexual',
  12: 'music', 13: 'song', 14: 'biography', 15: 'history',
  16: 'war', 17: 'west', 18: 'fantasy', 19: 'adventure',
  20: 'disaster', 21: 'martial', 22: 'erotic'
};
export default {
  name: "MoviesNav",
  data() {
    return {
      dateOrRate:'1',
      cid:'0',
      types: categories
    }
  },
  methods: {
    handleSelect(value) {
      this.cid = value;
      this.$emit('categorySelect')
    },
    handleSelect2(value) {
      this.dateOrRate = value;
      this.$emit('categorySelect')
    }
  }
}
</script>

<style scoped>
  div {
    display: flex;
    flex-wrap: wrap;
  }

  button {
    background: transparent !important;
    color: white;
    border: none;
    margin: 0 !important;
  }

  .el-radio {
    color: white;
  }

  /deep/ .chooseBytype span.el-radio__inner {
    display: none;
  }

  .el-radio {
    font-size: 25px;
    padding-top: 10px;
  }
</style>
