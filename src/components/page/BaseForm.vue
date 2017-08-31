<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-date"></i> 表单</el-breadcrumb-item>
        <el-breadcrumb-item>基本表单</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="form-box">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="活动名称" prop="name">
          <el-input v-model="ruleForm.name"></el-input>
        </el-form-item>
        <el-form-item label="活动区域" prop="region">
          <el-select v-model="ruleForm.region" placeholder="请选择活动区域">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="活动时间" prop="date1">
          <el-col :span="11">
            <el-date-picker type="date" placeholder="选择日期" v-model="ruleForm.date1" style="width: 100%;"></el-date-picker>
          </el-col>
          <el-col class="line" :span="2">-</el-col>
          <el-col :span="11">
            <el-time-picker type="fixed-time" placeholder="选择时间" v-model="ruleForm.date2" style="width: 100%;"></el-time-picker>
          </el-col>
        </el-form-item>
        <el-form-item label="即时配送">
          <el-switch on-text="" off-text="" v-model="ruleForm.delivery"></el-switch>
        </el-form-item>
        <el-form-item label="活动性质">
          <el-checkbox-group v-model="ruleForm.type">
            <el-checkbox label="美食/餐厅线上活动" name="type"></el-checkbox>
            <el-checkbox label="地推活动" name="type"></el-checkbox>
            <el-checkbox label="线下主题活动" name="type"></el-checkbox>
            <el-checkbox label="单纯品牌曝光" name="type"></el-checkbox>
          </el-checkbox-group>
        </el-form-item>
        <el-form-item label="特殊资源">
          <el-radio-group v-model="ruleForm.resource">
            <el-radio label="线上品牌商赞助"></el-radio>
            <el-radio label="线下场地免费"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="活动形式">
          <el-input type="textarea" v-model="ruleForm.desc"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
          <el-button>取消</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-cascader :placeholder="placeholder" :options="options" @change="handleChange">
    </el-cascader>
  </div>
</template>

<script>
import {
  Select, Option, Cascader
} from 'element-ui'
import AreaData from 'area-data';
export default {
  props: {
    placeholder: {
      type: String,
      default: '请选择'
    },
    type: {
      type: String,
      default: 'code', //  code-返回行政区域代码 text-返回文本 all-返回 code 和 text
      validator: (val) => ['all', 'code', 'text'].indexOf(val) > -1
    },
    level: {
      type: Number,
      default: 0, // 0->二联 1->三联 
      validator: (val) => [0, 1].indexOf(val) > -1
    }
  },
  data() {
    return {
      ruleForm: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      },
      options: [],
      rules: {
        name: [
          { required: true, message: '请输入活动名称', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        region: [
          { required: true, message: '请选择活动区域', trigger: 'change' }
        ],
        date1: [
          { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
        ],
        date2: [
          { type: 'date', required: true, message: '请选择时间', trigger: 'change' }
        ],
        type: [
          { type: 'array', required: true, message: '请至少选择一个活动性质', trigger: 'change' }
        ],
        resource: [
          { required: true, message: '请选择活动资源', trigger: 'change' }
        ],
        desc: [
          { required: true, message: '请填写活动形式', trigger: 'blur' }
        ]
      }
    };
  },
  install(Vue) {
    Vue.use(Select);
    Vue.use(Option);
    Vue.use(Cascader);
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!');
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    getAreaText(selected) {
      const texts = [];
      const provinces = AreaData['86'];

      for (let i = 0, l = selected.length; i < l; i++) {
        switch (i) {
          case 0:
            texts.push(provinces[selected[i]]);
            break;
          case 1:
            const city = AreaData[selected[0]][selected[i]];
            texts.push(city);
            break;
          case 2:
            const area = AreaData[selected[1]][selected[i]];
            texts.push(area);
            break;
        }
      } 
      return texts;
    }, 
    getAll(selected) {
      const all = [];
      const texts = this.getAreaText(selected);
      if (texts.length === selected.length) {
        alert('获取数据出错了');
      } 
      for (let i = 0, l = texts.length; i < l; i++) {
        const item = {
          [selected[i]]: texts[i]
        };
        all.push(item);
      } 
      return all;
    }, 
    handleChange(selected) {
      console.log('handlechange', selected);
      if (this.type === 'code') {
        this.$emit('input', selected);
      } else if (this.type === 'text') {
        this.$emit('input', this.getAreaText(selected));
      } else {
        this.$emit('input', this.getAll(selected));
      }
    }, 
    iterate(obj) {
      const temp = [];
      for (const key in obj) {
        temp.push({
          label: obj[key],
          value: key
        });
      }
      return temp;
    }, 
    iterateCities() {
      const temp = [];
      const provinces = this.iterate(AreaData['86']); 
      for (let i = 0, l = provinces.length; i < l; i++) {
        const item = {};
        item['label'] = provinces[i].label;
        item['value'] = provinces[i].value;

        item['children'] = this.iterate(AreaData[provinces[i].value]);
        temp.push(item);
      } 
      return temp;
    }, 
    iterateAreas() {
      const temp = [];
      const cities = this.iterateCities(); 
      for (let i = 0, c = cities.length; i < c; i++) {
        const city = cities[i];
        for (let j = 0, l = cities[i].children.length; j < l; j++) {
          const item = cities[i].children[j];
          item['children'] = this.iterate(AreaData[cities[i].children[j].value]);
        }
        temp.push(city);
      } 
      return temp;
    }
  },
  created() {
    // if (this.level === 0) {
    //     this.options = this.iterate(AreaData['86']);
    //     return;
    // }

    if (this.level === 0) {
      this.options = this.iterateCities();
      return;
    }

    if (this.level === 1) {
      this.options = this.iterateAreas();
      return;
    }
  }
}
</script>