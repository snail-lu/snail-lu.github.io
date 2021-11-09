---
title: element-ui中表格穿梭功能
date: 2020-05-08 20:33:33
summary: element-ui中表格穿梭功能实现
categories:
- [随笔]
- [前端]
- [功能实现]
tags:
- Vue
- element-ui
- el-table
---

### 一、功能描述
- **实现**： 表格数据的穿梭功能实现。
- **版本**： `element-ui: "^2.3.7"`。

### 二、代码实现
1. `el-table`上监听`selection-change`事件；
2. `添加`按钮事件处理函数中进行数据去重合并；
3. `删除`按钮事件处理函数中进行数据删除操作。
``` vue
<template>
    <el-row type="flex">
        <el-col :span="10">
            <el-row>待选区</el-row>
            <el-table
                :data="table1Data"
                style="width: 100%"
                ref="table1"
                stripe
                @selection-change="onTable1Select">
                <el-table-column
                    type="selection"
                    width="55">
                </el-table-column>
                <el-table-column
                    prop="name"
                    label="姓名"
                    width="180">
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="地址">
                </el-table-column>
            </el-table>
        </el-col>
        <el-col :span="4">
            <el-button type="primary" @click="onAdd">添加</el-button>
            <el-button type="primary" @click="onDelete">删除</el-button>
        </el-col>
        <el-col :span="10">
            <el-row>已选区</el-row>
            <el-table
                :data="table2Data"
                style="width: 100%"
                ref="table2"
                strip
                @selection-change="onTable2Select">
                <el-table-column
                    type="selection"
                    width="55">
                </el-table-column>
                <el-table-column
                    prop="name"
                    label="姓名"
                    width="180">
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="地址">
                </el-table-column>
            </el-table>
        </el-col>
    </el-row>
</template>

<script>
    export default {
        data() {
            return {
                table1Data: [{
                    id: '1',
                    name: '张帅',
                    address: '南京市秦淮区秦虹路98号',
                }, {
                    id: '2',
                    name: '王帅',
                    address: '北京市朝阳区东三环北路甲26号',
                }, {
                    id: '3',
                    name: '刘帅',
                    address: '上海市浦东新区潍坊西路与浦城路交叉路口往西北约50米',
                }, {
                    id: '4',
                    name: '孟帅',
                    address: '湖北省武汉市硚口区解放大道586号',
                }],
                table2Data: [],
                selectedTable1Data: [], // table1已选数据
                selectedTable2Data: []  // table2已选数据
            }
        },
        methods: {
            /**
             * table1选择事件处理函数
             * @param {array} rows 已勾选的数据
             */
            onTable1Select(rows) {
                this.selectedTable1Data = [...rows];
            },

            /**
             * table2选择事件处理函数
             * @param {array} rows 已勾选的数据
             */
            onTable2Select(rows) {
                this.selectedTable2Data = [...rows];
            },

            /**
             * 添加按钮事件处理函数
             */
            onAdd() {
                this.filterAdd(this.selectedTable1Data, this.table2Data, 'id');
                this.selectedTable1Data = [];
                this.$refs.table1.clearSelection();
            },

            /**
             * 删除按钮事件处理函数
             */
            onDelete() {
                this.table2Data = this.filterDelete(this.selectedTable2Data, this.table2Data, 'id');
                this.selectedTable2Data = [];
            },

            /**
             * 根据选中项去重添加到array中
             * @param {array} records   待添加数据
             * @param {array} targetRecords   目标数据
             * @param {string} compareProperty  对比的重复属性
             * @param {boolean} isEnd   往尾部添加？默认往头部添加
             */
            filterAdd (records=[], targetRecords=[], compareProperty, isEnd = false) {
                const o = new Set();
                targetRecords.forEach(record=>{
                    o.add(record[compareProperty]);
                })
                records.forEach(record=>{
                    if(!o.has(record[compareProperty])) {
                        if (isEnd) {
                            targetRecords.push(record);
                        } else {
                            targetRecords.unshift(record);
                        }
                    }
                })
            },

            /**
             * 删除数组中数据
             * @param {array} records   待删除数据
             * @param {array} targetRecords   目标数据
             * @param {string} compareProperty  对比的重复属性
             * @return {array} 删除待删除数据后的目标数据
             */
            filterDelete (records=[], targetRecords=[], compareProperty) {
                const o = new Set();
                records.forEach(record=>{
                    o.add(record[compareProperty]);
                })

                return targetRecords.filter((item) => !o.has(item[compareProperty]))
            }
        },
    }
</script>

<style lang="scss" scoped>

</style>
```

### 三、最终效果
[demo地址](https://snail-lu.github.io/vue-demo/#/example/tabletransfer)