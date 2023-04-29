<template>
    <div>
        <div class="select_options">
          <div class="select_option" v-for="(option, index) in filteredOptions" :key="index" :class="{ 'is-selected': isSelected(option),'select_option_disabled': option.disabled }" @click="selectOption(option)">
            <div class="select_option-checkbox" v-if="multiple">
              <input type="checkbox" :disabled="option.disabled" :checked="isSelected(option)" @change.stop>
            </div>
            <div class="select_option-text" :class="{'select_option-text_disabled':option.disabled}">
              <!-- 自定义显示需要显示值 -->
              <slot name="option" :option="option">{{ option.text }}</slot>
            </div>
          </div>
          <div class="select_no-results" v-if="!filteredOptions.length">无搜索结果</div>
        </div>
    </div>
</template>
<script>
export default {
  name: 'L-option'
}
</script>
<style lang="scss">
.select_options {
     display: flex;
     flex-wrap: wrap;
        .select_no-results {
            padding: 10px;
            color: #ccc;
  }
    .select_option_disabled {
     display: flex;
     align-items: center;
     width: 100%;
     height: 36px;
     padding: 0 10px;
     cursor: $disabled;
     user-select: none;
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
        .select_option-checkbox {
            margin-right: 6px;
        }
        .select_option-text {
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
    }
    .select_option:hover,
    .select_option.is-selected {
    background-color: #eee;
    }
}
</style>
