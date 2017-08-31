<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-date"></i> 表单</el-breadcrumb-item>
                <el-breadcrumb-item>图片上传</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="content-title">支持拖拽</div>
        <div class="plugins-tips">
            Element UI自带上传组件。 访问地址：
            <a href="http://element.eleme.io/#/zh-CN/component/upload" target="_blank">Element UI Upload</a>
        </div>
        <croppa v-model="myCroppa"></croppa>
    </div>
</template>

<script>
import 'vue-croppa/dist/vue-croppa.css'
import Vue from 'vue'
import Croppa from 'vue-croppa'
Vue.use(Croppa)  
new Vue({
   // ... other vm options omitted
   data: {
     myCroppa: {}
   },

   methods: {
     uploadCroppedImage() {
       this.myCroppa.generateBlob((blob) => {
         // write code to upload the cropped image file (a file is a blob)
       }, 'image/jpeg', 0.8) // 80% compressed jpeg file
     }
   }
 })     
</script>


