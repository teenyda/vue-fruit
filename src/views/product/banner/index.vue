<template>
  <div class="app-container">
    <div class="block">
      <!-- <span class="demonstration">Click 指示器触发</span> -->
      <el-carousel trigger="click" height="300px" @change="onChange">
        <el-carousel-item v-for="(item,index) in bannerList" :key="index" autoplay="false">
          <h3 v-if="!item.image" class="small">{{ item }}</h3>
          <el-image v-if="item.image" :src="item.image" @click="toProduct(item.productId)"></el-image>
        </el-carousel-item>
      </el-carousel>

      <div>
        <el-alert
          title="商城首页轮播图"
          type="success"
          description="推荐数量：5张 格式推荐： 900 x 383px"
          :closable="false">
        </el-alert>

      </div>

      <el-alert
        v-if="associated"
        title="幻灯片已关联产品！"
        type="info"
        :description="productDescription"
        show-icon>
      </el-alert>
      <el-alert
        v-else
        title="幻灯片无产品关联，请前往商品管理！"
        type="error"
        :description="productDescription"
        show-icon>
      </el-alert>
      <!-- <div class="banner-info">
        <span>幻灯片{{bannerIndex + 1}}</span>
        <span>商品ID{{ productId }}</span>
        <span>商品名称{{ productName }}</span>
      </div> -->

      <div class="edit">
        <el-button type="primary" icon="el-icon-edit" circle @click="bannerEdit"></el-button>
        <!-- <el-button type="warning" icon="el-icon-plus" circle @click="bannerAdd"></el-button> -->
        <el-button type="danger" icon="el-icon-delete" circle @click="bannerDelete"></el-button>
      </div>

    </div>

    <el-dialog
      title="上传幻灯片"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <span>选择方式</span>
      <div style="text-align: center;margin-top: 40px;">
        <el-button type="primary" @click="uploadClick">上传<i class="el-icon-upload el-icon--right"></i></el-button>

        <el-button type="primary" @click="linkVisible = true; dialogVisible = false">外链<i class="el-icon-link el-icon--right"></i></el-button>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <!-- <el-button type="primary" @click="dialogVisible = false">确 定</el-button> -->
      </span>
    </el-dialog>

    <!-- 上传窗口 -->
    <el-dialog
      title="提示"
      :visible.sync="uploadVisible"
      width="30%">
      <div>
        <!-- :http-request="uploadImg" -->
        <el-upload
          class="upload-demo"
          action="http://localhost:9000/api/app/file/upload"
          name="file"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :file-list="fileList"
          :limit="1"
          :on-success="handleAvatarSuccess"
          :on-error="handleAvatarError"
          :before-upload="beforeAvatarUpload"
          list-type="picture">
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过2MB</div>
        </el-upload>
      </div>
      <span slot="footer" class="dialog-footer">
        <!-- <el-button>取 消</el-button> -->
        <el-button type="primary" @click="uploadVisible = false">确 定</el-button>
      </span>
    </el-dialog>

    <!-- 外链添加窗口 -->
    <el-dialog
      title="提示"
      :visible.sync="linkVisible"
      width="30%">
      <div>
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入外链"
          v-model="textarea">
        </el-input>
      </div>
      <div style="margin-top: 15px">
        <span class="demonstration">预览</span>
        <el-image :src="textarea" @load="loadSuccess" @error="loadError">
          <div slot="error" class="image-slot">
            <i class="el-icon-picture-outline"></i>
          </div>
        </el-image>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button @click="linkVisible = false">取 消</el-button>
        <el-button type="primary" @click="addBannerFromLink">确 定</el-button>
      </span>
    </el-dialog>

  </div>
</template>

<script>
import { getBanners, addBanner, updateBanner } from '@/api/banner'

export default {
  data() {
    return {
      bannerList: ['无幻灯片，请添加', '无幻灯片，请添加', '无幻灯片，请添加'],
      bannerCount: 5,
      bannerIndex: 0,
      // 用于控制bannerIndex是否改变
      bannerIndexLocked: false,
      productId: 0,
      productName: '',
      dialogVisible: false,
      uploadVisible: false,
      linkVisible: false,
      title: '',
      textarea: '',
      bannerImageFlag: false,
      fileList: []
    }
  },

  computed: {
    productDescription: function() {
      if (this.checkIsBanner()) {
        const banner = this.getBannerByIndex()
        return `幻灯片ID: ${banner.bannerId} | 关联产品ID: ${banner.productId}`
      }
      // this.$router.push(`/product/productInfo/${productId}`)
    },
    associated: function() {
      if (this.checkIsBanner()) {
        const banner = this.getBannerByIndex()
        return banner.productId
      }
      return false
    }
  },

  methods: {
    onChange(index) {
      // console.log(index)
      // 没有锁就更换index
      if (!this.bannerIndexLocked) {
        this.bannerIndex = index
      }
      const banner = this.bannerList[index]
      if (banner && banner.productId) {
        this.productId = banner.productId
        this.productName = banner.productName
      } else {
        this.productId = ''
        this.productName = ''
      }
    },

    toProduct(productId) {
      productId && this.$router.push(`/product/productInfo/${productId}`)
    },

    bannerEdit() {
      this.dialogVisible = true
      // 锁着index
      this.bannerIndexLocked = true
    },

    bannerDelete() {

    },

    checkIsBanner() {
      const banner = this.getBannerByIndex()
      const isObj = Object.prototype.toString.call(banner) === '[Object Object]'
      return typeof banner === 'object'
    },

    getBannerByIndex() {
      return this.bannerList[this.bannerIndex]
    },

    uploadClick() {
      this.fileList = []
      this.dialogVisible = false
      this.uploadVisible = true
    },

    // 手动上传（也可以）
    // uploadImg(fileObj) {
    //   let formData = new FormData();
    //   formData.set("file", fileObj.file);
    //   console.log(fileObj.file);
    //   uploadImage(formData)
    //     .then(res => {
    //       console.log(res)
    //     })
    //     .catch()

    //   // axios
    //   //   .post('接口地址', formData, {
    //   //     headers: {
    //   //       "Content-type": "multipart/form-data"
    //   //     }
    //   //   }).then().catch();
    // },

    handleRemove(file, fileList) {
      console.log(file, fileList)
    },
    handlePreview(file) {
      console.log(file)
    },

    // 上传成功
    handleAvatarSuccess(res, file) {
      // ??
      // this.imageUrl = URL.createObjectURL(file.raw)
      console.log(res)
      this.bannerImageFlag = true
      const imgUrl = res.data.fileDownloadUrl
      this.judgeBanner(imgUrl)
      this.uploadVisible = false
    },
    handleAvatarError(err, file, fileList) {
      this.bannerImageFlag = false
    },
    beforeAvatarUpload(file) {
      const isJPGorPNG = file.type === 'image/jpeg' || file.type === 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPGorPNG) {
        this.$message.error('上传头像图片只能是 JPG 或 PNG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPGorPNG && isLt2M
    },

    ajaxGetBanner() {
      return [
        {
          productId: 12345,
          productName: '新疆葡萄价格美丽',
          image: 'https://img.zcool.cn/community/013de756fb63036ac7257948747896.jpg'
        }
      ]
    },

    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    },

    loadSuccess(e) {
      console.info('图片加载成功', this.textarea)
      this.bannerImageFlag = true
    },

    loadError(e) {
      console.error('图片加载失败', this.textarea)
      this.bannerImageFlag = false
    },

    // 添加图片链接
    addBannerFromLink() {
      this.judgeBanner(this.textarea)
    },

    judgeBanner(imgUrl) {
      const banner = this.bannerList[this.bannerIndex]
      const isObj = Object.prototype.toString.call(banner) === '[Object Object]'
      if (typeof banner === 'object') {
        // 修改幻灯片
        this.setBanner(banner, imgUrl)
      } else {
        // 添加幻灯片1
        this.setBanner(null, imgUrl)
      }
    },

    setBanner(banner, imgUrl) {
      if (this.bannerImageFlag) {
        if (banner) {
          // api
          banner.image = imgUrl
          updateBanner(banner)
            .then(data => {
              this.bannerList.splice(this.bannerIndex, 1, data)
              // 解锁index
              this.bannerIndexLocked = false
            })
            .finally(() => {
              this.linkVisible = false
            })
        } else {
          // 添加
          addBanner({image: imgUrl})
            .then(data => {
              this.bannerList.splice(this.bannerIndex, 1, data)
              // 解锁index
              this.bannerIndexLocked = false
            })
            .finally(() => {
              this.linkVisible = false
            })
        }
      } else {
        this.$message.error('图像加载失败，请重试!')
      }
    },

  },
  created() {
    getBanners()
      .then(data => {
        const banners = data
        const length = this.bannerCount - banners.length
        // 幻灯片数量不足用文字代替图片
        // console.log(length)
        for (let i = 0; i < length; i++) {
          banners.push('无幻灯片，请添加')
        }
        this.bannerList = banners

        const banner = this.bannerList[0]
        if (banner && banner.productId) {
          this.productId = banner.productId
          this.productName = banner.productName
        }

      })


    // getBook()
    //   .then(res => {
    //     console.log(res)
    //   })

    // addBook({'bookName': 'sbsbs'})
    //   .then(res => {
    //     console.log(res)
    //   })
  }

}
</script>

<style scoped>
  .block {
    width: 80%;
    margin: 0 auto;
  }
  .el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 300px;
    margin: 0;
    text-align: center;
  }

  .el-carousel__item:nth-child(2n) {
     background-color: #99a9bf;
  }

  .el-carousel__item:nth-child(2n+1) {
     background-color: #d3dce6;
  }
  .banner-info {
    color: #8c8c8c;
    size: 15px;
    margin-top: 10px;
  }

  .banner-info span {
    margin-left: 20px;
  }

  .edit {
    text-align: center;
  }
</style>
