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
            难度：
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
          <el-col :span="6">4444</el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>

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
      // 学科列表数据，用于给下拉列表展示使用
      subjectIDList: [],

      // difficultyList: difficultyList, // 难度，完整赋值
      difficultyList, // 难度，简易成员赋值，注意,不要赋予默认值
      questionTypeList, // 试题类型，简易成员赋值，注意,不要赋予默认值

      // 题库搜索表单数据对象
      searchForm: {
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '' // 试题类型
      }
    }
  },
  created() {
    // 学科
    this.getSubjectIDList()
  },
  methods: {
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
</style>
