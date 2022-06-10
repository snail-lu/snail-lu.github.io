---
title: element-ui中表格自定义排序
date: 2020-05-07 16:54:03
summary: element-ui中表格自定义排序
tags:
- Vue
- element-ui
- el-table
categories:
- [项目开发]
---

### 一、功能描述
- 实现： 对于一次性拉取所有数据、前端来做分页的表格使用场景，使用`el-table-column`自带的`sortable`只能对当前页数据进行排序，这里简单实现el-table的自定义排序，排序完成后再做分页即可（本文省略）。
- 版本： `"element-ui": "^2.3.7"`。

### 二、代码实现
1. `el-table`上监听`sort-change`事件；
2. `el-table-column`上`sortable`属性设置为`custom`；
3. `el-table-column`上`sort-orders`属性设置为`['ascending','descending']`。（默认取值为`['ascending','descending',null]`）
``` vue
<template>
    <el-table
        :data="tableData"
        stripe
        style="width: 50%;margin: 0 auto;"
        @sort-change="onSortChange">
        <el-table-column
            prop="date"
            label="日期"
            width="180"
            sortable="custom"
            :sort-orders="['ascending','descending']">
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
        <el-table-column
            prop="sort"
            label="排序"
            sortable="custom"
            :sort-orders="['ascending','descending']">
        </el-table-column>
    </el-table>
</template>

<script>
  export default {
    data() {
        return {
            tableData: [{
                date: '2014-12-02',
                name: '张帅',
                address: '南京市秦淮区秦虹路98号',
                sort: 100,
            }, {
                date: '2016-06-04',
                name: '王帅',
                address: '北京市朝阳区东三环北路甲26号',
                sort: 15,
            }, {
                date: '2012-03-01',
                name: '刘帅',
                address: '上海市浦东新区潍坊西路与浦城路交叉路口往西北约50米',
                sort: 8
            }, {
                date: '2018-11-03',
                name: '孟帅',
                address: '湖北省武汉市硚口区解放大道586号',
                sort: 1
            }]
        }
    },
    methods: {
        /**
         * 表格排序事件处理函数
         * @param {object} {column,prop,order} 列数据|排序字段|排序方式
         */
        onSortChange({ prop, order }) {
            this.tableData.sort(this.compare(prop,order));
        },

        /**
         * 排序比较
         * @param {string} propertyName 排序的属性名
         * @param {string} sort ascending(升序)/descending(降序)
         * @return {function}
         */
        compare (propertyName, sort) {
            return function (obj1, obj2) {
                var value1 = obj1[propertyName]
                var value2 = obj2[propertyName]
                if (typeof value1 === 'string' && typeof value2 === 'string') {
                    const res = value1.localeCompare(value2, 'zh')
                    return sort === 'ascending' ? res : -res
                } else {
                    if (value1 <= value2) {
                        return sort === 'ascending' ? -1 : 1
                    } else if (value1 > value2) {
                        return sort === 'ascending' ? 1 : -1
                    }
                }
            }
        }
    },
  }
</script>

<style lang="scss" scoped>

</style>
```
### 三、最终效果
[demo地址](https://portfolio-snail.vercel.app/#/demo/table-sort)