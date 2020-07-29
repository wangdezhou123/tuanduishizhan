<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <!-- 
        el-row:设置布局行
          :gutter="xx"  设置列彼此的间距，单位是px像素
        el-col:设置布局列
          :span: 设置各个列占据的宽度，满值24，是“权”的单位
          :offset="6" 设置当前列左边的空隙宽度，单位是“权” 
        -->
        <el-row :gutter="20">
          <el-col :span="6">
            学科：
            <el-select v-model="searchForm.subjectID" placeholder="请选择" class="wd">
              <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            难&nbsp;&nbsp;&nbsp;&nbsp;度：
            <el-select v-model="searchForm.difficulty" placeholder="请选择" class="wd">
              <el-option
                v-for="item in difficultyList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            试题类型：
            <el-select v-model="searchForm.questionType" placeholder="请选择" class="wd">
              <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :value="item.value"
                :label="item.label"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签：
            <el-select v-model="searchForm.tags" placeholder="请选择" class="wd">
              <el-option
                v-for="item in tagsList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">
            城市：
            <el-select v-model="searchForm.province" placeholder="选城市" style="width:90px;">
              <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select v-model="searchForm.city" placeholder="选地区" style="width:90px;"></el-select>
          </el-col>
          <el-col :span="6">
            关键字：
            <el-input type="text" v-model="searchForm.keyword" class="wd"></el-input>
          </el-col>
          <el-col :span="6">
            题目备注：
            <el-input type="text" v-model="searchForm.remarks" class="wd"></el-input>
          </el-col>
          <el-col :span="6">
            企业简称：
            <el-input type="text" v-model="searchForm.shortName" class="wd"></el-input>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">
            方向：
            <el-select v-model="searchForm.direction" placeholder="请选择" class="wd">
              <el-option v-for="item in directionList" :key="item" :value="item" :label="item"></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            录入人：
            <el-select v-model="searchForm.creatorID" placeholder="请选择" class="wd">
              <el-option
                v-for="item in creatorIDList"
                :key="item.id"
                :label="item.username"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            二级目录：
            <el-select v-model="searchForm.catalogID" placeholder="请选择" class="wd">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            <el-button size="mini">清除</el-button>
            <el-button type="primary" size="mini">搜索</el-button>
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>

import { provinces, citys } from '@/api/hmmm/citys' // 获取 省份/城市 信息方法导入

import { direction as directionList } from '@/api/hmmm/constants'

import { simple as directorysSimple } from '@/api/hmmm/directorys' // 获取二级目录信息方法导入

import { simple as usersSimple } from '@/api/base/users' // 获取录入人信息方法导入

import { simple as tagsSimple } from '@/api/hmmm/tags' // 获取标签信息方法导入

// 导入api函数，获得简单学科列表信息
import { simple } from '@/api/hmmm/subjects.js'
// 导入 难度、题型 的api常量
// 对导入进来的成员做别名设置，以便方便后续使用
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants.js'

export default {
  name: 'QuestionsList',
  data() {
    return {
      // 获得 省份 信息，简易成员赋值
      provinces, // provinces:provinces
      directionList, // 方向
      catalogIDList: [], // 二级目录
      creatorIDList: [], // 录入人
      tagsList: [], // 标签
      // 学科列表数据，用于给下拉列表展示使用
      subjectIDList: [],

      // difficultyList: difficultyList, // 难度，完整赋值
      difficultyList, // 难度，简易成员赋值，注意,不要赋予默认值
      questionTypeList, // 试题类型，简易成员赋值，注意,不要赋予默认值

      // 题库搜索表单数据对象
      searchForm: {
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 标签
        province: '', // 城市
        city: '', // 地区
        keyword: '', // 关键字
        remarks: '', // 备注
        shortName: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  created() {
    // 获得 二级目录 信息
    this.getCatalogIDList()
    // 获得录入人信息
    this.getCreatorIDList()
    // 获得标签信息
    this.getTagsList()
    // 学科
    this.getSubjectIDList()
  },
  methods: {
    // 获得 录入人 列表数据
    async getCatalogIDList() {
      var result = await directorysSimple()
      console.log(result)

      this.catalogIDList = result.data
    },

    // 获得 录入人 列表数据
    async getCreatorIDList() {
      var result = await usersSimple()
      this.creatorIDList = result.data
    },

    // 获得 标签 列表数据
    async getTagsList() {
      var result = await tagsSimple() // Promise对象 变为 dt了
      this.tagsList = result.data
    },

    // 获得下拉列表 学科列表的信息
    async getSubjectIDList() {
      // axios获得学科列表信息，不要使用“原生”的axios，
      // 调用“api函数”间接执行axios
      // api函数： @/api/*
      // 当前试题对应的api函数库文件： @/api/hmmm/questions.js
      // data接收

      // 通过api方法，获得简单学科列表信息(即进行axios请求)
      let result = await simple()
      // data接收信息
      this.subjectIDList = result.data
    }
  }
}
</script>

<style scoped>
.wd {
  width: 170px;
}
.el-row {
  margin-bottom: 10px;
}
</style>
