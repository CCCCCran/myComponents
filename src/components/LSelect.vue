<template>
     <div class="box">
      <div class="select"  ref="select">
      <div class="select_control" @click="toggleMenu">
        <div class="select_selected-value" v-if="!multiple">{{ selectedOption ? selectedOption.lable : placeholder }}
          <span class="select_remove-option" style="margin-left:180px" @click="selectedOption=''">×</span>
        </div>
        <div class="select_selected-values" v-else>
          <div class="select_selected-value"  id="close" v-for="(option, index) in selectedOptions" :key="index">
            {{ option.lable }}
            <span class="select_remove-option" @click="removeOption(option)">×</span>
          </div>
          <div class="select_selected-placeholder" v-if="!selectedOptions.length">{{ placeholder }}</div>
        </div>
        <div v-if="!menuOpen" class="select_arrow"></div>
        <div v-if="menuOpen" class="select_arrow_down"></div>
      </div>
      <div class="select_menu" v-show="menuOpen">
        <div class="select_search" v-if="searchable">
          <slot name="search" >
          <input type="text" class="select_search-input" placeholder="搜索" v-model="searchQuery" @input="search">
          </slot>
        </div>
          <div class="select_options">
          <div class="select_option" v-for="(option, index) in filteredOptions" :key="index" :class="{ 'is-selected': isSelected(option),'select_option_disabled': option.disabled }" @click="selectOption(option)">
            <div class="select_option-checkbox" v-if="multiple">
              <input type="checkbox" :disabled="option.disabled" :checked="isSelected(option)" @change.stop>
            </div>
            <div class="select_option-text" :class="{'select_option-text_disabled':option.disabled}">
              <!-- 自定义显示需要显示值 -->
              <slot name="option" :option="option">{{ option.lable }}</slot>
            </div>
          </div>
          <div class="select_no-results" v-if="!filteredOptions.length">无搜索结果</div>
        </div>
      </div>
    </div>
     </div>
</template>
<script>

export default {
  model: {
    value: 'value',
    event: 'valueChange'
  },
  props: {
    // 传入数据
    options: {
      type: Array,
      required: true
    },
    // 父组件赋默认值
    value: {
      type: [String, Array, Number, Object],
      default: ''
    },
    // 提示语
    placeholder: {
      type: String,
      default: '请选择'
    },
    // 是否可以多选
    multiple: {
      type: Boolean,
      default: false
    },
    // 是否可以搜索
    searchable: {
      type: Boolean,
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    },
    replaceKey: {
      type: Object,
      default: () => {
        return {
          value: 'value', lable: 'text', disabled: 'disabled'
        }
      }
    },
    // 想选择搜索的类型
    searchType: {
      type: String,
      default: 'lable'
    },
    filterMethod: {
      type: Function,
      default: () => {}
    }
  },
  data () {
    return {
      newOptions: [],
      menuOpen: false,
      // 单选存储
      selectedOption: null,
      // 多选存储
      selectedOptions: [],
      // 搜索关键词
      searchQuery: '',
      comVal: ''
    }
  },
  computed: {
    filteredOptions () {
      if (this.$parent.searchQuery) {
        return this.newOptions.filter(option => option[this.searchType].includes(this.$parent.searchQuery))
      } else {
        return this.newOptions.filter(option => option[this.searchType].includes(this.searchQuery))
      }
    }
  },
  watch: {
    // 处理传过来的数据
    options: {
      immediate: true,
      deep: true,
      handler (newVal) {
        this.newOptions = newVal.map(item => {
          return {
            lable: item[this.replaceKey.lable],
            value: item[this.replaceKey.value],
            disabled: item[this.replaceKey.disabled]
          }
        }
        )
      }
    },
    // 侦听父组件value值
    value: {
      immediate: true,
      handler (newVal) {
        this.comVal = this.multiple ? typeof newVal === 'string' ? [newVal] : newVal : newVal
        if (newVal instanceof Object) {
          if (!Array.isArray(newVal)) {
            newVal = 'lable' in newVal ? newVal.lable : []
          } else {
            newVal = newVal.map(item => item.toString())
          }
        }
        if (this.multiple) {
          // 传过来数字值的处理
          newVal = typeof newVal === 'number' ? newVal.toString() : newVal
          this.selectedOptions = newVal ? this.newOptions.filter(option => newVal.includes(option.lable)) : []
        } else {
          this.selectedOption = newVal ? this.newOptions.find(option => option.lable === newVal) : null
        }
      }
    },
    comVal: {
      deep: true,
      handler (newVal) {
        this.$emit('valueChange', newVal)
      }
    },
    menuOpen (val) {
      // 选项栏打开回调
      this.$emit('menuOpen', val)
    }
  },
  created () {
    window.addEventListener('click', this.documentClick)
  },
  beforeDestroy () {
    window.removeEventListener('click', this.documentClick)
  },
  methods: {
    documentClick (e) {
      if (this.menuOpen && !this.getIsElementdOut(e.target, this.$refs.select)) {
        this.menuOpen = false
      }
    },
    toggleMenu () {
      this.menuOpen = !this.menuOpen
      if (this.menuOpen) {
        this.$nextTick(() => {
          // 置顶
          this.$refs.select.scrollTop = 0
        })
      }
    },
    // 选中值
    selectOption (option) {
      if (option.disabled) {
        return ''
      } else {
        if (this.multiple) {
          if (this.isSelected(option)) {
            this.selectedOptions = this.selectedOptions.filter(item => item.lable !== option.lable)
          } else {
            this.selectedOptions = this.selectedOptions.concat(option)
          }
        } else {
          this.selectedOption = option
          this.menuOpen = false
        }
        if (this.multiple) {
          this.comVal = this.selectedOptions.map(m => m.lable)
          this.$nextTick(() => {
            this.$emit('change', this.selectedOptions)
          })
        } else {
          this.comVal = this.selectedOption.lable
          this.$nextTick(() => {
            this.$emit('change', this.selectedOption)
          })
        }
      }
    },
    // 删除值
    removeOption (option) {
      const index = this.selectedOptions.findIndex(item => item.lable === option.lable)
      if (index !== -1) {
        this.selectedOptions.splice(index, 1)
        this.menuOpen = true
      }
      this.comVal = this.selectedOptions.map(m => m.lable)
      this.$nextTick(() => {
        // 值改变回调
        this.$emit('change', this.selectedOptions)
        // 删除值改变回调
        this.$emit('remove', option.lable)
      })
    },
    // 是否已经被选中
    isSelected (option) {
      return this.selectedOptions.find(item => item.lable === option.lable) !== undefined
    },
    search () {
      // 返回搜索关键词回调
      this.$emit('search', this.searchQuery)
    },
    // 判断是否点击组件之外的元素
    getIsElementdOut (element, box) {
      const newElement = typeof element === 'string' ? document.querySelector(element) : element
      const newBox = typeof box === 'string' ? document.querySelector(box) : box
      if (!newElement) return false
      let current = newElement.parentNode
      while (current && [current].indexOf(newBox) < 0) {
        current = current.parentNode
      }
      // 点中就返回ture
      return [current].indexOf(newBox) > -1
    }
  }
}
</script>
<style lang="scss">
$get:pointer;
$disabled:not-allowed;
.select {
  position: relative;
  width: 270px;
  font-size: 14px;
  font-family: Arial, sans-serif;
  color: #333;
  .select_control {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 36px;
  padding: 0 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: $get;
  user-select: none;
}

  .select_control:hover {
  border-color: #aaa;
  }

  .select_arrow {
  width: 0;
  height: 0;
  margin-left: 6px;
  border-style: solid;
  border-width: 5px 4px 0 4px;
  border-color: #333 transparent transparent transparent;
}
  .select_arrow_down {
  width: 0;
  height: 0;
  margin-left: 6px;
  border-style: solid;
  border-width:  0 4px 5px 4px;
  border-color: transparent transparent #333 transparent;
  }

  .select_selected-value,
  .select_selected-values {
  flex: 1;
  }

.select_selected-value {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.select_selected-values {
  display: flex;
  flex-wrap: wrap;
}

.select_selected-values .select_selected-value {
  display: inline-flex;
  align-items: center;
  background-color: #eee;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 6px;
  margin-bottom: 6px;
  padding: 2px 6px;
}

.select_selected-values .select_remove-option {
  display: inline-block;
  margin-left: 6px;
  font-weight: bold;
  font-size: 16px;
  line-height: 1;
  cursor: $get;
}

.select_selected-values .select_remove-option:hover {
  color: #f00;
}

.select_selected-placeholder {
  color: #ccc;
}

.select_menu {
  position: absolute;
  top: 50px;
  left: 0;
  z-index: 1;
  width: 100%;
  max-height: 200px;
  overflow-y: auto;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px
}

.select_search {
  padding: 5px;
}

.select_search-input {
  width: 220px;
  padding: 6px 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  outline: none;
}

.select_options {
  display: flex;
  flex-wrap: wrap;
  .select_option_disabled {
  display: flex;
  align-items: center;
  width: 100%;
  height: 36px;
  padding: 0 10px;
  cursor: $disabled;
  user-select: none;
  }
}

.select_option {
  display: flex;
  align-items: center;
  width: 100%;
  height: 36px;
  padding: 0 10px;
  cursor: $get;
  user-select: none;
    .select_option-text_disabled {
      color: #cfd0d3;
    }
}

.select_option:hover,
.select_option.is-selected {
  background-color: #eee;
}

.select_option-checkbox {
  margin-right: 6px;
}

.select_option-text {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.select_no-results {
  padding: 10px;
  color: #ccc;
}
}

</style>
