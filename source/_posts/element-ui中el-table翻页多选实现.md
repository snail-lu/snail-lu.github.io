---
title: element-ui中el-table翻页多选实现
date: 2021-11-11 21:19:57
summary: element-ui中el-table翻页多选实现
tags:
- Vue
- element-ui
- el-table
categories:
- [项目开发]
---

### 一、功能描述
- 实现： 后台管理系统中，经常使用`el-table`并配合`el-pagiation`实现表格翻页功能，但是由于每页数据都是翻页后重新从后端拉取的，上一页勾选的数据在翻页后`el-table`组件并不会存储，需要自行实现。
- 版本： `"element-ui": "^2.14.1"`。

### 二、代码实现
1. `el-table`上监听`select`及`select-all`事件，进行当前页数据的`单选/全选`操作；
2. `el-pagination`上监听`size-change`及`current-change`，进行分页器的页码和页数操作，同时将当前页数据进行存储；
3. 每页重新拉取数据后，需要将当前页数据已选数据恢复选中状态，主要使用表格的`toggleRowSelection`方法。
``` vue
<template>
  <div class="root">
      <el-table
        ref="multipleTable"
        :data="tableData"
        border
        height="450"
        :header-cell-style="{ background: '#F5F7FA', color: '#606266', textAlign: 'center' }"
        :cell-style="{ textAlign: 'center' }"
        @select="handleSelectionChange"
        @select-all="handleSelectionChange"
      >
        <el-table-column
          type="selection"
        />
        <el-table-column
          prop="id"
          label="id"
          align="center"
        />
        <el-table-column
          prop="goodsName"
          label="商品名称"
          align="center"
        />
        <el-table-column
          prop="goodsCode"
          label="款号"
          align="center"
        />
        <el-table-column
          prop="goodsPrice"
          label="吊牌价格（元）"
          align="center"
        />
      </el-table>
      <el-pagination
        background
        style="text-align:right"
        layout="total,prev,sizes, pager, next"
        :total="goodsTotal"
        :page-size="goodsForm.pageInfo.pageSize"
        :current-page="goodsForm.pageInfo.pageNum"
        @size-change="handleGoodsSizeChange"
        @current-change="handleGoodsPageChange"
        :page-sizes="[5,10]"
      />
      <el-row>
          <el-button @click="getAllSelectedGoodsId">打印所有已选数据</el-button>
          {{ selectedGoodsIdsAll }}
      </el-row>
  </div>
</template>

<script>
export default {
  name: 'TableSelect',
  data() {
    return {
      tableData: [],
      goodsTotal: 0,
      tableLoading: false,
      selectedGoodsIds: [], // 当前页中已选数据
      selectedGoodsIdsOther: [], // 其它页中已选的数据
      selectedGoodsIdsAll: [], // 所有已选数据
      goodsForm: {
          pageInfo: {
              pageNum: 1,
              pageSize: 5
          }
      }
    }
  },
  created() {
    // this.onSearch()
    this.onSearchGoods()
  },
  methods: {
    // 切换页码
    handleGoodsPageChange(currentPage) {
      this.getAllSelectedGoodsId()
      this.goodsForm.pageInfo.pageNum = currentPage
      this.onSearchGoods()
    },
    // 切换每页条数
    handleGoodsSizeChange(currentPage) {
      this.getAllSelectedGoodsId()
      this.goodsForm.pageInfo.pageSize = currentPage
      this.onSearchGoods()
    },

    // 搜索商品
    onSearchGoods() {
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)',
        target: '.loadingtext'
      });
      this.queryGoods(this.goodsForm).then(
        (res) => {
          this.tableData = res.list
          this.toggleSelection()
          this.goodsTotal = res.total
          loading.close();
        },
        () => {
          this.tableLoading = false
          loading.close();
        }
      )
    },

    // 设置表格选中效果
    toggleSelection() {
      const selectedGoodsIds = []
      const selectedGoodsIdsOther = this.selectedGoodsIdsAll.slice()
      this.$nextTick(()=>{
        this.tableData.forEach(goods => {
          if(this.selectedGoodsIdsAll.includes(goods.id)){
            this.$refs.multipleTable.toggleRowSelection(goods, true);
            // 拆分当前页数据和其它页数据
            selectedGoodsIds.push(goods.id)
            const index = selectedGoodsIdsOther.indexOf(goods.id)
            selectedGoodsIdsOther.splice(index, 1)
          }
        });
        this.selectedGoodsIds = selectedGoodsIds
        this.selectedGoodsIdsOther = selectedGoodsIdsOther
      })
    },

    // 商品选择
    handleSelectionChange(tableData) {
      this.selectedGoodsIds = tableData.map(goods => goods.id)
    },

    // 获得全部数据
    getAllSelectedGoodsId() {
      const { selectedGoodsIds, selectedGoodsIdsOther } = this
      this.selectedGoodsIdsAll = [...selectedGoodsIdsOther, ...selectedGoodsIds]
    },

    // 模拟接口获取数据
    queryGoods(params) {
        const { pageInfo } = params
        const { pageNum, pageSize } = pageInfo
        const data = [
            {  id: 1, goodsName: '测试商品1', goodsCode: '000001', goodsPrice: 101 }, 
            {  id: 2, goodsName: '测试商品2', goodsCode: '000002', goodsPrice: 102 }, 
            {  id: 3, goodsName: '测试商品3', goodsCode: '000003', goodsPrice: 103 }, 
            {  id: 4, goodsName: '测试商品4', goodsCode: '000004', goodsPrice: 104 }, 
            {  id: 5, goodsName: '测试商品5', goodsCode: '000005', goodsPrice: 105 },
            {  id: 6, goodsName: '测试商品6', goodsCode: '000006', goodsPrice: 106 }, 
            {  id: 7, goodsName: '测试商品7', goodsCode: '000007', goodsPrice: 107 }, 
            {  id: 8, goodsName: '测试商品8', goodsCode: '000008', goodsPrice: 108 }, 
            {  id: 9, goodsName: '测试商品9', goodsCode: '000009', goodsPrice: 109 }, 
            {  id: 10, goodsName: '测试商品10', goodsCode: '0000010', goodsPrice: 110 }
        ]
        return new Promise((resolve)=>{
            setTimeout(()=>{
                resolve({
                    list: data.slice((pageNum-1)*pageSize, pageNum*pageSize),
                    total: data.length
                })
            },1000)
        })
    }
  }
}
</script>
```

### 三、最终效果
[demo地址](https://snaillu.gitee.io/portfolio-vue/demo/table-select)