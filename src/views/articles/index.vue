<template>
    <el-card class='articles'>
        <bread-crumb slot="header">
            <template slot="title">文章列表</template>
        </bread-crumb>
        <!-- 放置一个表单容器 -->

        <el-form style="padding-left:50px">
            <el-form-item label="文章状态:">
                <el-radio-group v-model="searchForm.status">
                    <el-radio :label="5">全部</el-radio>
                    <el-radio :label="0">草稿</el-radio>
                    <el-radio :label="1">待审核</el-radio>
                    <el-radio :label="2">审核通过</el-radio>
                    <el-radio :label="3">审核失败</el-radio>

                </el-radio-group>
                <!-- {{searchForm}} -->
            </el-form-item>
            <el-form-item label="频道列表:">
                <!-- {{channels}} -->
                <el-select placeholder="请选择频道" v-model="searchForm.channel_id">
                    <!-- label 是显示值 value是存储值 -->
                    <el-option v-for="item in channels" :key="item.id" :label="item.name" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="时间选择:">
                <el-date-picker v-model="searchForm.dateRange" type="daterange"></el-date-picker>
            </el-form-item>
            <el-row class="total" type="flex" align="middle">
              <span>共找到XX条符合条件的内容</span>
            </el-row>
            <div class="article-item" v-for="item in 10" :key="item">
                <!-- 左侧 -->
                <div class="left">
                  <img src="../../assets/img/header.jpg" alt="">
                  <div class="info">
                    <span>1</span>
                    <el-tag class="tag">标签2</el-tag>
                    <span class="date">时间</span>
                  </div>
                </div>
                <!-- 右侧 -->
                <div class="right">
                  <span><i class="el-icon-edit"></i>修改</span>
                  <span><i class="el-icon-edit"></i>删除</span>
                </div>
            </div>
        </el-form>
    </el-card>
</template>

<script>
export default {
  data () {
    return {
      searchForm: {
        status: 5, // 默认选中全部
        channel_id: null, // 默认不选中任何一个
        dateRange: [] // 日期范围
      },
      channels: []// 接收频道数据
    }
  },
  methods: {
    // 获取所有的频道
    getChannels () {
      this.$axios({
        url: '/channels'
      }).then(result => {
        this.channels = result.data.channels
      })
    }
  },
  created () {
    this.getChannels() // 获取文章数据
  }

}
</script>

<style lang='less' scoped>
  .articles{
    .total{
      height:60px;
      border-bottom:1px dashed #ccc;
    }
    .article-item{
      display: flex;
      justify-content: space-between;
      padding:10px 0;
      border-bottom: 1px solid #f2f3f5;
      .left{
        display: flex;
        img{
          width: 150px;
          height: 90px;
          border-radius: 4px;
        }
        .info{
          margin-left: 10px;
          display: flex;
          flex-direction: column;
          justify-content: space-around;
          .date{
            color: #999;
            font-size: 12px;
          }
          .tag{
            text-align: center;
            width: 60px;
          }
        }
      }
      .right{
        span{
          font-size: 14px;
          margin-right: 8px;
          cursor: pointer;
        }
      }
    }
  }

</style>
