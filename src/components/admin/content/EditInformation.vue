<template>
  <div>
    <el-dialog
        title="Add/modify movie information"
        :visible.sync="dialogFormVisible"
        @close="clear">
      <el-form v-model="form" style="text-align: left" ref="dataForm">
        <el-form-item label="movie title" :label-width="formLabelWidth" prop="title">
          <el-input v-model="form.title" autocomplete="off" placeholder="Please enter a movie name"></el-input>
        </el-form-item>
        <el-form-item label="release date" :label-width="formLabelWidth" prop="date">
          <el-input v-model="form.date" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item class="rate-item" label="score" :label-width="formLabelWidth" prop="rate">
          <el-input v-model="form.rate" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="director" :label-width="formLabelWidth" prop="director">
          <el-input v-model="form.director" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="editor" :label-width="formLabelWidth" prop="scriptwriter">
          <el-input v-model="form.scriptwriter" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="actor" :label-width="formLabelWidth" prop="actors">
          <el-input v-model="form.actors" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="type" :label-width="formLabelWidth" prop="categories">
          <el-tag
              style="margin: 3px"
              :key="category.id"
              v-for="category in form.categories"
              closable
              :disable-transitions="false"
              @close="handleClose(category.type)">
            {{category.type}}
          </el-tag>
          <el-input
              class="input-new-tag"
              v-if="categoriesInputVisible"
              v-model="categoriesInputValue"
              ref="saveTagInput"
              size="small"
              @keyup.enter.native="handleInputConfirm"
              @blur="handleBlur"
          >
          </el-input>
          <el-button v-else class="button-new-tag" size="small" @click="showInput">+ new label</el-button>
        </el-form-item>
        <el-form-item label="country/region" :label-width="formLabelWidth" prop="district">
          <el-input v-model="form.district" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="language" :label-width="formLabelWidth" prop="language">
          <el-input v-model="form.language" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="duration" :label-width="formLabelWidth" prop="duration">
          <el-input v-model="form.duration" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="Movie Synopsis" :label-width="formLabelWidth" prop="abs">
          <el-input type="textarea" v-model="form.abs" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="海报" :label-width="formLabelWidth" prop="cover">
          <el-input v-model="form.cover" autocomplete="off" placeholder="海报 URL"></el-input>
          <img-upload @onUpload="uploading" ref="imgUpload"></img-upload>
        </el-form-item>
        <el-form-item prop="id" style="height: 0">
          <el-input type="hidden" v-model="form.id" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">cancel</el-button>
        <el-button type="primary" @click="onSubmit">ensure</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import ImgUpload from "@/components/admin/content/ImgUpload";
export default {
  name: "EditInformation",
  components: {ImgUpload},
  data () {
    return {
      typeToId: {
        1: 'plot', 2: 'comedy', 3: 'action',
        4: 'love', 5: 'sci-fi', 6: 'cartoon', 7: 'suspenseful',
        8: 'thriller', 9: 'fear', 10: 'crime', 11: 'homosexual',
        12: 'music', 13: 'song', 14: 'biography', 15: 'history',
        16: 'war', 17: 'west', 18: 'fantasy', 19: 'adventure',
        20: 'disaster', 21: 'martial', 22: 'erotic'
      },
      categoriesChanged:'false',
      categoriesInputVisible: false,
      categoriesInputValue: '',
      dialogFormVisible: false,
      form: {
        id: '',
        title: '',
        date: '',
        rate: '',
        director: '',
        scriptwriter: '',
        actors: '',
        district: '',
        language: '',
        duration: '',
        cover: '',
        abs: '',
        categories: []
      },
      formLabelWidth: '120px'
    }
  },
  methods: {

    findKey (obj,value, compare = (a, b) => a === b){
      return Object.keys(obj).find(k => compare(obj[k], value))
    },
    clear () {
      this.categoriesChanged = 'false'
      this.form = {
        id: '',
        title: '',
        date: '',
        rate: '',
        director: '',
        scriptwriter: '',
        actors: '',
        district: '',
        language: '',
        duration: '',
        cover: '',
        abs: '',
        categories: []
      }
    },
    onSubmit () {
      this.$axios
          .post('admin/content/movie/update'+'?changeCategories='+this.categoriesChanged, {
            id: this.form.id,
            cover: this.form.cover,
            title: this.form.title,
            director: this.form.director,
            scriptwriter: this.form.scriptwriter,
            actors: this.form.actors,
            language: this.form.language,
            district: this.form.district,
            rate: this.form.rate,
            date: this.form.date,
            abs: this.form.abs,
            duration: this.form.duration,
            categories: this.form.categories
          }).then(resp => {
            if (resp && resp.status === 200) {
              this.dialogFormVisible = false
              this.$emit('onSubmit')
        }
      }).catch((failureResponse)=>{
        this.$alert('请求失败')
      })
    },
    handleClose(tag) {
      console.log('delete before' + this.form.categories)
      this.categoriesChanged = 'true';
      this.form.categories = this.form.categories.filter(category=>category.type != tag)
      console.log('delete after' + this.form.categories)
    },

    showInput() {
      this.categoriesInputVisible = true;
      this.$nextTick(() => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },
    handleBlur() {
      this.categoriesInputVisible = false;
      this.categoriesInputValue = '';
    },
    handleInputConfirm() {
      let inputValue = this.categoriesInputValue;
      let flag = (Object.values(this.typeToId).indexOf(inputValue))
      if (flag === -1) {
        this.$confirm("不支持的类型","注意",{
          type:'warning'
        });
      } else {
        let new_category = {id: this.findKey(this.typeToId,inputValue),type:inputValue};
        console.log('add before'+ this.form.categories)
        this.form.categories.push(new_category);
        console.log('add after' + this.form.categories)
        this.categoriesChanged = 'true';
      }
      this.categoriesInputVisible = false;
      this.categoriesInputValue = '';
    },
    uploading() {
      this.form.cover = this.$refs.imgUpload.url
    }
  }
}
</script>

<style scoped>
  .add-movie {
    margin: 50px 0 0 20px;
    font-size: 100px;
    float: left;
    cursor: pointer;
  }

  /deep/ .el-input__inner {
    color: black !important;
  }

  /deep/ .rate-item lable.el-form-item__label {
    margin-top: 5px !important;
  }
</style>
