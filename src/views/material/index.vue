<template>
  <el-card v-loading="loading">
    <!-- 头部内容 -->
    <bread-crumb slot="header">
      <template slot="title">素材管理</template>
    </bread-crumb>
    <!-- 上传 -->
    <el-row type='flex' justify="end">
      <el-upload action="" :http-request="uploadImg" :show-file-list="false">
        <el-button size="small" type="primary">上传图片</el-button>
      </el-upload>
    </el-row>

    <!-- 标签页 -->
    <el-tabs v-model="activeName" @tab-click="changeTab">
      <!-- 标签 -->
      <el-tab-pane label="全部图片" name="all">
        <!-- 生成页面结构 -->
        <div class="img-list">
          <!-- v-for对数据进行遍历 -->
          <el-card class="img-card" v-for="item in list" :key="item.id">
            <img :src="item.url" alt />
            <el-row class="operate" type="flex" align="middle" justify="space-around">
              <!-- 需要根据当前是否收藏的状态来决定 是否给字体颜色 -->
              <i @click="collectOrCancel(item)" :style="{ color: item.is_collected ? 'red' : '#000' }" class="el-icon-star-on"></i>
              <i @click="delMaterial(item.id)" class="el-icon-delete-solid"></i>
            </el-row>
          </el-card>
        </div>
      </el-tab-pane>
      <el-tab-pane label="收藏图片" name="collect">
        <div class="img-list">
          <!-- v-for对数据进行遍历 -->
          <el-card class="img-card" v-for="item in list" :key="item.id">
            <img :src="item.url" alt />
          </el-card>
        </div>
      </el-tab-pane>
    </el-tabs>
    <!-- 公共分页组件 -->
    <el-row type="flex" justify="center">
      <el-pagination
        :current-page="page.currentPage"
        :page-size="page.pageSize"
        :total="page.total"
        @current-change="changePage"
        background
        layout="prev, pager, next"
      ></el-pagination>
    </el-row>
  </el-card>
</template>

<script>
export default {
  data () {
    return {
      loading: false,
      activeName: 'all', // 当前选中的标签
      list: [], // 接收素材数据
      page: {
        currentPage: 1,
        pageSize: 8,
        total: 0
      }
    }
  },
  methods: {
    // 删除用户图片素材
    delMaterial (id) {
      this.$confirm('你确定要删除此图片吗?').then(() => {
        this.$axios({
          method: 'delete',
          url: `/user/images/${id}`
        }).then(() => {
          this.getMaterial() // 重新拉取数据
        })
      })
    },
    // 取消或者收藏
    collectOrCancel (item) {
      // item.iscollected true => 取消收藏 ? 收藏
      this.$axios({
        method: 'put',
        url: `/user/images/${item.id}`,
        data: {
          collect: !item.is_collected // 取反 因为 收藏  =>取消收藏
        }
      }).then(result => {
        this.getMaterial() // 重新拉取数据
      })
    },
    // 上传图片方法
    uploadImg (params) {
      this.loading = true // 先弹个层
      let data = new FormData()
      data.append('image', params.file) // 文件加入到参数中
      this.$axios({
        method: 'post',
        url: '/user/images',
        data
      }).then(result => {
        this.loading = false // 关闭弹层
        this.getMaterial() // 直接调用拉取数据的方法
      })
    },
    // 改变页码方法
    changePage (newPage) {
      this.page.currentPage = newPage
      this.getMaterial()
    },
    // 切换页签方法
    changeTab () {
      this.page.currentPage = 1 // 重置回第一页
      this.getMaterial() // 调用获取数据方法
    },
    // 获取素材的方法
    getMaterial () {
      this.$axios({
        url: '/user/images',
        params: {
          page: this.page.currentPage,
          per_page: this.page.pageSize,
          collect: this.activeName === 'collect' // 传false是获取所有的数据 传true是获取收藏数据
        }
      }).then(result => {
        this.list = result.data.results // 获取图片数据 有可能是 全部 也由可能是收藏
        this.page.total = result.data.total_count // 总条数
      })
    }
  },
  created () {
    this.getMaterial()
  }
}
</script>

<style lang='less' scoped>
.img-list {
  display: flex;
  flex-wrap: wrap;
  .img-card {
    width: 200px;
    height: 220px;
    margin: 20px 50px;
    position: relative;
    img {
      width: 100%;
      height: 100%;
    }
    .operate {
      width: 100%;
      position: absolute;
      left: 0;
      bottom: 0;
      font-size: 20px;
      height: 36px;
      background-color: #f4f5f6;
      i {
        cursor: pointer;
      }
    }
  }
}
</style>
