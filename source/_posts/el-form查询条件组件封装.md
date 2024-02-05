---
title: el-form查询条件组件封装
date: 2024-02-05 15:16:51
tags: element-ui
---


#### 大致代码如下
```js
<template>
    <div class="search-form-component" :style="`margin-bottom: ${marginBottom}px;`">
      <el-form
        ref="form"
        :class="!autoSearch && 'flex-form'"
        size=""
        :model="form"
        inline
      >
        <div
          ref="searchFormZone"
          :class="['zone-form-items']"
          :style="currentHeight && `height: ${currentHeight};`"
        >
          <el-form-item
            v-for="(item, index) in formData"
            :key="index"
            class="form-item"
            :label="item.label"
            :label-width="(isLockLabelWidth ? lockLabelWidthValue : (item.labelWidth || labelWidth)) + 'px'"
          >
            <el-input
              v-if="item.type === 'input'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              :clearable="item.clearable || true"
              :placeholder="`请输入${item.label}`"
              @input="changeValue($event, item.prop)"
              @keyup.enter.native="doSearch"
            />
            <el-select
              v-else-if="item.type === 'select'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              :multiple="item.multiple || false"
              :collapse-tags="item.collapseTag || true"
              :placeholder="`请选择${item.label}`"
              :clearable="item.clearable || true"
               :disabled="item.disabled"
              :filterable="item.filterable || true"
              @change="changeValue($event, item.prop)"
            >
              <el-option
                v-for="(ops, idx) in selectOptions(item)"
                :key="idx"
                :label="ops[(item.selectProps && item.selectProps.label) ? item.selectProps.label : 'label']"
                :value="ops[(item.selectProps && item.selectProps.value) ? item.selectProps.value : 'value']"
              />
            </el-select>
            <TypeSelect
              v-else-if="item.type === 'typeSelect'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              :search-param="item.searchParam || item.label"
              :url-name="item.urlName || 'getDictByType'"
              :placeholder-lable="item.label"
              @handleChange="changeValue($event, item.prop)"
            />
            <ElDragSelect
              v-else-if="item.type === 'ElDragSelect'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              url-name="listBaseType"
              :placeholder-lable="item.label"
              :show-option="item.showOption"
            />
  
            <el-date-picker
              v-else-if="item.type === 'daterange'"
              v-model="form[item.prop]"
              :clearable="item.clearable || true"
              :type="item.datetime ? 'datetimerange' : 'daterange'"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              value-format="timestamp"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              :default-time="['00:00:00', '23:59:59']"
              @change="changeValue($event, item.prop, item.type)"
            />
            <el-date-picker
              v-else-if="item.type === 'date'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              :type="item.datetime ? 'datetime' : 'date'"
              value-format="timestamp"
              :placeholder="`请选择${item.label}`"
              @change="changeValue($event, item.prop)"
            />
            <el-time-picker
              v-else-if="item.type === 'time'"
              v-model="form[item.prop]"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              value-format="HH:mm"
              format="HH:mm"
              :placeholder="`请选择${item.label}`"
              @change="changeValue($event, item.prop)"
            />
  
            <el-input
              v-else-if="item.type === 'dialogSelect'"
              v-model="form[item.prop]"
              :placeholder="`请选择${item.label}`"
              :style="{'width': `${(item.width || searchWidth)}px`}"
              readonly
            >
              <el-button slot="append" icon="el-icon-search" />
            </el-input>
          </el-form-item>
        </div>
        <!-- 右侧固定按钮 -->
        <div v-if="!autoSearch" :class="['zone-search', isFoldable && 'foldable']">
          <el-form-item class="form-item">
            <el-button
              v-if="isFoldable"
              type="text"
              :icon="isFold ? 'el-icon-arrow-down' : 'el-icon-arrow-up'"
              @click="doToggleFold"
            >
              {{ isFold ? '展 开' : '收 起' }}
            </el-button>
            <el-button icon="el-icon-search" type="primary" @click="doSearch">搜索</el-button>
            <el-button icon="el-icon-refresh" @click="resetForm">重置</el-button>
          </el-form-item>
        </div>
      </el-form>
    </div>
  </template>
  
  <script>
  export default {
    name: 'SearchForm',
    props: {
      value: {
        type: Object,
        default: () => ({})
      },
      formData: {
        /** 格式
           * [{ label: "label", prop: "prop", type: "input" }]
           * ps: label、prop、type 必传
           * */
        type: Array,
        required: true
      },
      formName: {
        type: String,
        default: 'query'
      },
      labelWidth: {
        type: Number,
        default: 85
      },
      // 是否锁定label的宽度（锁定宽度可实现上下对齐）
      isLockLabelWidth: {
        type: Boolean,
        default: false
      },
      // 锁定宽度值
      lockLabelWidthValue: {
        type: Number,
        default: 75
      },
      marginBottom: {
        type: Number,
        default: 0
      },
      autoSearch: {
        // 是否开启自动搜素
        type: Boolean,
        default: false
      },
      // 超出一行后是否默认折叠
      folded: {
        type: Boolean,
        default: true
      },
      // 点击重置时不需要清空的参数
      resetExcludesParams: {
        type: Array,
        default: () => []
      },
      // 父组件的查询事件名称
      queryMethodsName: {
        type: String,
        default: 'search'
      },
      searchWidth: {
        type: Number,
        default: 200
      }
    },
    data() {
      return {
        form: {},
        isFoldable: false,
        isFold: false,
        currentHeight: '',
        offsetHeight: 30
      }
    },
    watch: {
      value: {
        handler(v) {
          if (Object.keys(v).length) {
            this.form = { ...v }
          }
        },
        immediate: true,
        deep: true
      },
      isFold: {
        handler(newVal) {
          this.currentHeight = newVal ? '40px' : this.offsetHeight
        }
      }
    },
    mounted() {
      this.getFoldable()
      if (this.isFoldable) {
        this.isFold = this.folded
      }
      window.onresize = () => {
        this.currentHeight = ''
        this.$nextTick(this.getFoldable)
      }
    },
    beforeDestroy() {
      window.onresize = null
    },
    methods: {
      getFoldable() {
        if (!this.$refs.searchFormZone) return
        const offsetHeight = this.$refs.searchFormZone.offsetHeight
        this.offsetHeight = offsetHeight + 'px'
        this.isFoldable = !this.autoSearch && (offsetHeight > 60)
        this.currentHeight = this.isFold ? '60px' : this.offsetHeight
      },
      doToggleFold() {
        this.isFold = !this.isFold
      },
      doSearch() {
        if (this.$listeners['query']) {
          this.$emit('query')
        } else {
          this.$parent[this.queryMethodsName]()
        }
      },
      selectOptions(item) {
        const parentOptions = item.selectProps && item.selectProps.optionName
          ? item.selectProps.optionName
          : `${item.prop}Options`
          console.log(123123, this.$parent,this.$parent[parentOptions])
        return item.options || this.$parent[parentOptions]
      },
      changeValue(value, prop, type) {
        if (type === 'daterange' && Array.isArray(prop)) {
          const [start, end] = prop
          const val1 = Array.isArray(value) ? value[0] : null
          const val2 = Array.isArray(value) ? value[1] : null
          this.$set(this.$parent[this.formName], start, val1)
          this.$set(this.$parent[this.formName], end, val2)
        } else {
          this.$set(this.$parent[this.formName], prop, value)
        }
        this.$parent[this.formName].pageNo = 1
        if (this.autoSearch) {
          this.doSearch()
        }
      },
      resetForm() {
        for (const key in this.formData) {
          const { prop, label, defaultValue } = this.formData[key]
          if (!this.resetExcludesParams.includes(label)) {
            if (Array.isArray(prop)) {
              const [start, end] = prop
              this.$set(this.formData[key], 'dateRange', []) // 清空日期区间组件
              this.$set(this.$parent[this.formName], start, null)
              this.$set(this.$parent[this.formName], end, null)
            } else {
              this.$set(this.form, prop, null)
              this.$set(this.$parent[this.formName], prop, defaultValue || null)
            }
          }
        }
        this.$emit('reset')
        this.doSearch()
      },
      // 供父组件调用清空
      clearSearchItem(item) {
        this.$set(this.form, item, null)
      }
    }
  }
  </script>
  
    <style lang="scss" scoped>
      .search-form-component {
        background: #fff;
        padding: 0 20px 0 0;
        .form-item {
          margin: 4px 6px 4px 0;
          width: auto !important;
          display: inline-block;
        }
        .flex-form {
          display: flex;
          padding: 0;
        .zone-form-items {
            flex: 1;
            display: block;
            overflow-y: hidden;
            transition: height .3s;
        }
        .zone-search {
          width: 200px;
          &.foldable {
            width: 244px;
          }
          }
        }
      }
      </style>

```