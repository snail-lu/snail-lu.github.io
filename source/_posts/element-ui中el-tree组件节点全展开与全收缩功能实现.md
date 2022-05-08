---
title: element-ui中el-tree组件节点全展开与全收缩功能实现
date: 2020-05-05 15:08:10
summary: el-tree组件节点全展开与全收缩功能实现
tags:
- Vue
- element-ui
- el-tree
categories:
- [项目开发]
---

### 一、功能描述
- **实现**： element-ui中el-tree组件全部节点的展开与收缩功能。
- **版本**： "element-ui": "^2.3.7"。

### 二、代码实现
``` vue
<template>
  <div class="tree-container">
    <el-button type="primary" @click="onExpand">{{this.expandAll?'收缩':'展开'}}所有节点</el-button>
    <el-tree :data="data" ref="tree"></el-tree>
  </div>
</template>

<script>
  export default {
    data() {
      return {  
        expandAll: false,
        data: [{
          label: '一级 1',
          children: [{
            label: '二级 1-1',
            children: [{
              label: '三级 1-1-1'
            }]
          }]
        }, {
          label: '一级 2',
          children: [{
            label: '二级 2-1',
            children: [{
              label: '三级 2-1-1'
            }]
          }, {
            label: '二级 2-2',
            children: [{
              label: '三级 2-2-1'
            }]
          }]
        }]
      };
    },
    methods: {
      // 节点展开
      async onExpand () {
        try {
          await this.$confirm('确认要展开所有节点?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          })
          this.expandAll = !this.expandAll
          // 改变每个节点的状态
          this.changeTreeNodeStatus(this.$refs.tree.store.root)
        } catch (e) {
          this.$message({
            type: 'info',
            message: e === 'cancel' ? '取消操作' : '操作失败'
          })
        }
      },
      // 改变节点的状态
      changeTreeNodeStatus (node) {
        node.expanded = this.expandAll
        for (let i = 0; i < node.childNodes.length; i++) {
          // 改变节点的自身expanded状态
          node.childNodes[i].expanded = this.expandAll
          // 遍历子节点
          if (node.childNodes[i].childNodes.length > 0) {
            this.changeTreeNodeStatus(node.childNodes[i])
          }
        }
      }
    }
  };
</script>
```
### 三、最终效果
[demo地址](https://snail-lu.github.io/portfolio-vue/#/demo/tree-expand)