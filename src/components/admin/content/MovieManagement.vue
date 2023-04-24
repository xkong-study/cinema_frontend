<template>
  <div style="text-align: left">
    <el-row style="margin: 18px 0 0 18px; ">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/admin/dashboard' }">control center</el-breadcrumb-item>
        <el-breadcrumb-item>content management</el-breadcrumb-item>
        <el-breadcrumb-item>film management</el-breadcrumb-item>
      </el-breadcrumb>
    </el-row>
    <el-button class="add-button" type="success" @click="editMovie(emptyMovie)">add movie</el-button>
    <edit-information @onSubmit="loadMovies()" ref="edit"></edit-information>
    <el-card style="margin: 18px 2%;width: 95%">
      <el-table
          :data="movies"
          stripe
          ref="multipleTable"
          style="width: 100%"
          :default-sort="{prop:'date',order: 'descending'}"
          :max-height="tableHeight">
        <el-table-column
            fixed="left"
            type="selection"
            width="45">
        </el-table-column>
        <el-table-column
            fixed="left"
            type="expand">
          <template slot-scope="props">
            <el-form label-position="left" inline>
              <el-form-item>
                <span>{{ props.row.abs }}</span>
              </el-form-item>
            </el-form>
          </template>
        </el-table-column>
        <el-table-column
            fixed="left"
            prop="title"
            label="Movie title"
            fit>
        </el-table-column>
        <el-table-column
            prop="date"
            label="release date"
            sortable
            width="120">
        </el-table-column>
        <el-table-column
            prop="director"
            label="director"
        >
        </el-table-column>
        <el-table-column
            prop="scriptwriter"
            label="editor"
            width="100"
            fit>
        </el-table-column>
        <el-table-column
            prop="actors"
            label="starring"
            width="400"
            fit>
        </el-table-column>
        <el-table-column
          label="type"
          width="200"
        >
          <template slot-scope="props">
            <el-tag v-for="(category,i) in props.row.categories" :key="i">{{category.type}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="district"
            label="country/region"
        >
        </el-table-column>
        <el-table-column
            prop="language"
            label="language"
        >
        </el-table-column>
        <el-table-column
            prop="rate"
            sortable
            label="rate"
        >
        </el-table-column>
        <el-table-column
            prop="duration"
            label="duration"
        >
        </el-table-column>
        <el-table-column
            fixed="right"
            label="operate"
            width="operate">
          <template slot-scope="scope">
            <el-button
                @click.native.prevent="editMovie(scope.row)"
                type="text"
                size="small">
              edit
            </el-button>
            <el-button
                @click.native.prevent="deleteMovie(scope.row.id)"
                type="text"
                size="small">
              remove
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="margin: 20px 0 20px 0;float: left">
        <el-button @click="cancelSelect">cancel select</el-button>
        <el-button @click="batchDelete">batch deletion</el-button>
      </div>
    </el-card>
    <div style="margin: 20px 0 50px 0">
      <el-pagination
          background
          style="float:right;"
          layout="total, prev, pager, next, jumper"
          @current-change="handleCurrentChange"
          :page-size="pageSize"
          :total="1500">
      </el-pagination>
    </div>
  </div>
</template>

<script>
import EditInformation from "@/components/admin/content/EditInformation";
export default {
  name: 'MovieManagement',
  components: {EditInformation},
  data () {
    return {
      pageSize:21,
      emptyMovie: {
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
      movies: []
    }
  },
  mounted () {
    this.loadMovies()
  },
  computed: {
    tableHeight () {
      return window.innerHeight - 360
    }
  },
  methods: {
    loadMovies () {
      const _this = this;
      this.$axios.get('/movies/page/0').then(resp => {
        console.log(resp.status)
        console.log(resp.data)
        if (resp && resp.status === 200) {
          _this.movies = resp.data;
        }
      })
    },
    handleCurrentChange: function (currentPage) {
      const url = '/movies/page/' + (currentPage*this.pageSize);
      this.$axios.get(url).then(resp => {
        if(resp && resp.status === 200) {
          this.movies =resp.data;
        }
      })
    },
    deleteMovie (id) {
      this.$confirm('This operation will permanently delete the movie information, whether to continue?', 'reminder', {
        confirmButtonText: 'ensure',
        cancelButtonText: 'cancel',
        type: 'warning'
      }).then(() => {
            this.$axios
                .post('admin/content/movie/delete', {id: id}).then(resp => {
              if (resp && resp.status === 200) {
                this.loadMovies()
              }
            })
          }
      ).catch(() => {
        this.$message({
          type: 'info',
          message: 'Undeleted'
        })
      })
      // alert(id)
    },
    editMovie (item) {
      this.$refs.edit.dialogFormVisible = true
      this.$refs.edit.form = {
        id: item.id,
        cover: item.cover,
        title: item.title,
        date: item.date,
        rate: item.rate,
        director: item.director,
        scriptwriter: item.scriptwriter,
        actors: item.actors,
        language: item.language,
        district: item.district,
        duration: item.duration,
        abs: item.abs,
        categories: item.categories
      }
    },
    cancelSelect() {
      this.$refs.multipleTable.clearSelection();
    },
    batchDelete() {
      this.$alert("This feature is currently not supported.")
    }
  }
}
</script>

<style scoped>
.add-button {
  margin: 18px 0 0 10px;
}
</style>
