<template>
     <div class="select"  ref="select">
      <div class="select_control" @click="toggleMenu">
        <div class="select_selected-value" v-if="!multiple">{{ selectedOption ? selectedOption.text : placeholder }}
          <span class="select_remove-option" style="margin-left:180px" @click="selectedOption=''">×</span>
        </div>
        <div class="select_selected-values" v-else>
          <div class="select_selected-value" v-for="(option, index) in selectedOptions" :key="index">
            {{ option.text }}
            <span class="select_remove-option" @click="removeOption(option)">×</span>
          </div>
          <div class="select_selected-placeholder" v-if="!selectedOptions.length">{{ placeholder }}</div>
        </div>
        <div v-if="!menuOpen" class="select_arrow"></div>
        <div v-if="menuOpen" class="select_arrow_down"></div>
      </div>
      <div class="select_menu" v-show="menuOpen">
        <div class="select_search" v-if="searchable">
          <input type="text" class="select_search-input" placeholder="搜索" v-model="searchQuery" @input="search">
        </div>
        <div class="select_options">
          <div class="select_option" v-for="(option, index) in filteredOptions" :key="index" :class="{ 'is-selected': isSelected(option) }" @click="selectOption(option)">
            <div class="select_option-checkbox" v-if="multiple">
              <input type="checkbox" :checked="isSelected(option)" @change.stop>
            </div>
            <div class="select_option-text">{{ option.text }}</div>
          </div>
          <div class="select_no-results" v-if="!filteredOptions.length">无搜索结果</div>
        </div>
      </div>
    </div>
</template>
<script>
export default {
  props: {
    // 传入数据
    options: {
      type: Array,
      required: true
    },
    // 父组件赋默认值
    value: {
      type: String,
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
    }
  },
  data () {
    return {
      menuOpen: false,
      // 单选存储
      selectedOption: null,
      // 多选存储
      selectedOptions: [],
      // 搜索关键词
      searchQuery: '',
      // 选中值
      selectedValue: ''
    }
  },
  computed: {
    filteredOptions () {
      return this.options.filter(option => option.text.indexOf(this.searchQuery) !== -1)
    }
  },
  watch: {
    // 侦听父组件value值
    value: {
      immediate: true,
      handler (newVal) {
        if (this.multiple) {
          this.selectedOptions = newVal ? this.options.filter(option => newVal.includes(option.text)) : []
        } else {
          this.selectedOption = newVal ? this.options.find(option => option.text === newVal) : null
        }
      }
    }
  },
  methods: {
    toggleMenu () {
      this.menuOpen = !this.menuOpen
      if (this.menuOpen) {
        this.$nextTick(() => {
          // 置顶
          this.$refs.select.scrollTop = 0
        })
      }
      // 选项栏打开回调
      this.$emit('menuOpen', this.menuOpen)
    },
    // 选中值
    selectOption (option) {
      if (this.multiple) {
        if (this.isSelected(option)) {
          this.selectedOptions = this.selectedOptions.filter(item => item.value !== option.value)
        } else {
          this.selectedOptions = this.selectedOptions.concat(option)
        }
      } else {
        this.selectedOption = option
        this.menuOpen = false
      }
      if (this.multiple) {
        this.$emit('change', this.selectedOptions)
      } else {
        this.$emit('change', this.selectedOption)
      }
    },
    // 删除值
    removeOption (option) {
      const index = this.selectedOptions.findIndex(item => item.text === option.text)
      if (index !== -1) {
        this.selectedOptions.splice(index, 1)
      }
      // 值改变回调
      this.$emit('change', this.selectedValue)
      // 删除值改变回调
      this.$emit('remove', option.text)
    },
    // 是否已经被选中
    isSelected (option) {
      return this.selectedOptions.find(item => item.text === option.text) !== undefined
    },
    search () {
      // 返回搜索关键词回调
      this.$emit('search', this.searchQuery)
    }
  }
}
</script>
<style>
.select {
  position: relative;
  width: 270px;
  font-size: 14px;
  font-family: Arial, sans-serif;
  color: #333;
}

.select_control {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 36px;
  padding: 0 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
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
  cursor: pointer;
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
}

.select_option {
  display: flex;
  align-items: center;
  width: 100%;
  height: 36px;
  padding: 0 10px;
  cursor: pointer;
  user-select: none;
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
</style>
