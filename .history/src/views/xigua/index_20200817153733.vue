<template>
  <div class="app-container">
    <el-form ref="form" :model="form" label-width="120px">
      <el-form-item label="视频链接">
        <el-col :sm="{span:24}" :lg="{span:18}">
          <el-input v-model="form.url" />
        </el-col>
        <el-col :sm="{span:24}" :lg="{span:4}">
          <el-button type="primary" @click="onSubmit">解析</el-button>
          <el-button @click="onCancel">清空</el-button>
        </el-col>
      </el-form-item>

      <el-form-item label="解析地址">
        <el-input v-model="form.analysis" type="textarea" :rows="5"/>
      </el-form-item>
      
      <el-form-item label="视频作者">
        <el-input disabled>
          <template slot="prepend"><i class="el-icon-user-solid"></i></template>
        </el-input>
      </el-form-item>

      <el-form-item label="视频标题">
        <el-input disabled>
          <template slot="prepend"><i class="el-icon-user-solid"></i></template>
        </el-input>
      </el-form-item>
    </el-form>

    
  </div>
</template>

<script>

import { getVideo } from '@/api/xigua'

export default {
  name: 'Xigua',
  data() {
    return {
      // 视频链接
      url_list: [
        'm.toutiaoimg.cn',
        'www.ixigua.com',
        'm.ixigua.com'
      ],
      active: 0,
      activeName: 'first',
      form: {
        url: '',
        analysis:''
      },
      author: ''
    }
  },
  methods: {
    onSubmit() {
      this.parsingurl(this.form.url)
    },
    onCancel() {
      this.$message({
        message: 'cancel!',
        type: 'warning'
      })
    },
    parsingurl(url) {
      if (this.isContains(url)) {
        this.$message('success!')
        const data = {
          'videolink': url
        }
        getVideo(data).then(resp => {
          console.log(resp)
          this.form.analysis = resp.data
        })
      } else {
        this.$message({
          message: '链接错误!',
          type: 'warning'
        })
      }
    },
    isContains(url) {
      // 使用forEach return无效
      /* this.url_list.forEach(function(value, index, arr){
        if(url.indexOf(value) >= 0)
          return true
        })
        console.log(false)
        return false
      }
      使用 Array.some()
      arr.some(item => {
        console.log('b: ',item)
        return item === 2 // 当有数组有一项满足条件时结束并返回true
      })
      // 使用 Array.ervey()
      arr.every(item => {
        console.log('c: ',item)
        return item !== 2 // 检查数字中是否每一项都满足条件，如果有一项不满足就结束循环并返回false
      })
    */

      return this.url_list.some(item => url.indexOf(item) >= 0)
    }
  }
}
</script>

<style lang="scss" scoped>
  .dashboard {
    &-container {
      margin: 30px;
    }

    &-text {
      font-size: 30px;
      line-height: 46px;
    }
  }
</style>
