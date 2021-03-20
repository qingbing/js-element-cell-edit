# 封装 vue-element-ui 的组件
封装 cell 的编辑，目前主要用于 table-cell 中

### ====== 版本说明 ======
- 1.0.0 : 代码初始化
- 1.0.1 : 完善备注
  - 修改bug ： saveCallback的类型由“Object”改为“Function”
  - 添加 editable 属性，控制是否可编辑

## 1. 安装
```
npm install @qingbing/element-cell-edit
```

## 2. 参数介绍

| 参数名 | 参数类型 | 数据类型 | 必填 | 默认 |
|:---|:---|:---|:---|:---|
| property | 属性 | String | 是 | - |
| row | 属性 | Object | 是 | - |
| editable | 属性 | Boolean | 否 | true |
| saveCallback | 属性 | Function | 否 | undefined |
| params | 属性 | Object | 否 | {} |

## 3. 使用示例
```vue
<template>
  <div>
    <el-divider>控制表格编辑（元素编辑）{{ config }}{{editable}}</el-divider>
    <celledit
      property="isEdit"
      :row="config"
      :params="params.config"
    ></celledit>

    <el-divider>表格编辑</el-divider>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="ID" width="50"> </el-table-column>
      <el-table-column prop="username" label="用户名" width="150">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.username"
            :saveCallback="saveCallback"
          ></celledit>
        </template>
      </el-table-column>
      <el-table-column prop="name" label="姓名" width="100">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.name"
            :saveCallback="saveCallback"
          ></celledit>
        </template>
      </el-table-column>
      <el-table-column prop="sex" label="性别" width="100">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.sex"
            :saveCallback="saveCallback"
          ></celledit>
        </template>
      </el-table-column>
      <el-table-column prop="age" label="年龄" width="80">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.age"
            :saveCallback="saveCallback"
          ></celledit>
        </template>
      </el-table-column>
      <el-table-column prop="is_open" label="开放" width="80">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.is_open"
            :saveCallback="saveCallback"
          ></celledit>
        </template>
      </el-table-column>
      <el-table-column prop="info" label="个人信息">
        <template slot-scope="scope">
          <celledit
            :editable="editable"
            :property="scope.column.property"
            :row="scope.row"
            :params="params.info"
            :saveCallback="saveCallback"
          ></celledit> </template
      ></el-table-column>
    </el-table>
  </div>
</template>

<script>
// 导入包
import { merge } from "@qingbing/helper";
import { ajaxMethod } from "@/utils/unit";

export default {
  components: {
    ETable: () => import("@qingbing/element-table"),
    celledit: () => import("@qingbing/element-cell-edit"),
  },
  data() {
    const editParams = {
      dataType: "user",
      saveUrl: "/user-save",
    };
    return {
      config: {
        isEdit: "0",
      },
      params: {
        config: { type: "switch" },
        username: merge(editParams, { type: "text" }),
        name: merge(editParams, { type: "text" }),
        sex: merge(editParams, {
          type: "select",
          options: {
            0: "秘密",
            1: "男士",
            2: "女士",
          },
        }),
        age: merge(editParams, { type: "number" }),
        is_open: merge(editParams, { type: "switch" }),
        info: merge(editParams, { type: "textarea" }),
      },
      tableData: [
        {
          id: "1",
          username: "RzW",
          is_open: 1,
          name: "万磊",
          sex: "1",
          age: "22",
          info: "电的更看事众心中响求型可适千情。",
        },
        {
          id: "2",
          username: "s3Y",
          is_open: "0",
          name: "乔洋",
          sex: "2",
          age: "24",
          info: "置个京等拉己指林众通外手变意老。",
        },
      ],
    };
  },
  computed: {
    editable() {
      return "0" !== this.config.isEdit;
    },
  },
  methods: {
    saveCallback(cb, change, properties, params) {
      ajaxMethod(
        "user-cell-edit",
        merge({ id: properties.id }, change),
        "post",
        (res) => cb(res)
      );
    },
    isEditing() {
      return !!this.isEdit;
    },
  },

};
</script>
```
