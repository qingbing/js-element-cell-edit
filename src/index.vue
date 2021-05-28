<template>
  <div class="edit-cell" @click="handleEdit">
    <!-- switch -->
    <el-switch
      v-if="type == 'switch'"
      v-model="row[property]"
      :disabled="!editable"
      @change="handleBlur"
      active-value="1"
      inactive-value="0"
    >
    </el-switch>

    <!-- editing -->
    <template v-if="isEditing && editable">
      <!-- input-text -->
      <el-input
        v-if="type == 'text'"
        v-model="row[property]"
        @blur="handleBlur"
        v-focus
      ></el-input>

      <!-- textarea -->
      <el-input
        v-if="type == 'textarea'"
        v-model="row[property]"
        @blur="handleBlur"
        v-focus
        type="textarea"
      ></el-input>

      <!-- number -->
      <el-input-number
        v-if="type == 'number'"
        v-model="row[property]"
        @blur="handleBlur"
        v-focus
        style="width: 100%"
        :controls="false"
      ></el-input-number>

      <!-- select -->
      <el-select
        v-if="type == 'select'"
        v-model="row[property]"
        @blur="handleBlur"
        v-focus
        clearable
        style="width: 100%"
      >
        <el-option
          v-for="(label, value) in ops"
          :key="value"
          :label="label"
          :value="value"
        >
        </el-option>
      </el-select>
    </template>
    <!-- view -->
    <template v-if="!isEditing || !editable">
      <span v-if="type == 'text'">{{ row[property] }}</span>
      <span v-if="type == 'textarea'">{{ row[property] }}</span>
      <span v-if="type == 'number'">{{ row[property] }}</span>
      <span v-if="type == 'select'">{{ ops[row[property]] }}</span>
    </template>
  </div>
</template>

<script>
// 导入函数
import { isFunction, dump } from "@qingbing/helper";

// 导出
export default {
  props: {
    // 属性
    property: {
      type: String,
      require: true,
    },
    // 行记录
    row: {
      type: Object,
      require: true,
    },
    // 可编辑
    editable: {
      type: Boolean,
      default: true,
    },
    // 保存后台的方法， 相比 params.saveCallback， 该方法优先
    saveCallback: {
      type: Function,
      default: undefined,
    },
    // 传递的参数
    params: {
      type: Object,
      default: () => {
        return {};
      },
    },
  },
  data() {
    // 必须定义保存字段的方法
    if (isFunction(this.saveCallback)) {
      this.save = this.saveCallback;
    } else if (isFunction(this.params.saveCallback)) {
      this.save = this.params.saveCallback;
    }
    switch (this.params.type) {
      case "text":
      case "number": // 只能输入数字
      case "textarea":
      case "switch": // 只支持 "0", "1"；难得做值的自定义
        this.row[this.property] += "";
        break;
      case "select":
        this.ops = this.params.options ?? {};
        break;
      default:
        dump.error(`不支持的单元格编辑类型"${this.params.type}"`);
    }
    return {
      type: this.params.type,
      oldValue: "",
      isEditing: false,
    };
  },
  directives: {
    // 指令，当cell点击事件时，输入组件获取焦点
    focus: {
      inserted: function (el) {
        if (el.children[0].children[0]) {
          el.children[0].children[0].focus();
        } else if (el.children[0]) {
          el.children[0].focus();
        } else {
          el.focus();
        }
      },
    },
  },
  methods: {
    // 开启编辑
    handleEdit() {
      if (!this.editable) {
        return;
      }
      this.oldValue = this.row[this.property];
      this.isEditing = true;
    },
    // 释放编辑
    handleBlur() {
      // 如果焦点失去时值没有变化，就不需要有后续操作
      if (this.row[this.property] === this.oldValue) {
        this.afterSavecallback(true);
        return;
      }
      // 如果未设置保存方法，仅仅就是提供一个编辑的容器
      if (!isFunction(this.save)) {
        this.afterSavecallback(true);
        return;
      }
      // 制作修改的值，通过保存方法进行保存，由于需要用 afterSavecallback 函数来关闭编辑状态，这里把改函数放在第一个参数
      const changeProperty = {
        [this.property]: this.row[this.property],
      };
      this.save(this.afterSavecallback, changeProperty, this.row, this.params);
    },
    // 保存后回调
    afterSavecallback(status) {
      if (true !== status) {
        if ("switch" === this.type) {
          this.row[this.property] = this.row[this.property] ? "1" : "0";
        } else {
          this.row[this.property] = this.oldValue;
        }
      }
      this.isEditing = false;
    },
  },
};
</script>
<style scoped>
.edit-cell,
.edit-cell span {
  display: inline-block;
  height: 100%;
  width: 100%;
}
</style>