<template>
  <div class="home">
    <LSelect :options="options" :value="value" :multiple="true" :searchable="true
      " :lable="'lable'" @valueChange="input" :replaceKey="{value: 'value', lable: 'lable', disabled: 'disabled'}"
      >
    <!-- 选项栏插槽_自定义选项栏显示数据 -->
      <template slot="option" slot-scope="{option}">
        <div class="custom-option"  style= "display:inline-block">
          <span style="margin: 0 10px;">{{ option.lable }}</span>
          <span>{{ option.value }}</span>
        </div>
      </template>
      <!-- 自定义搜索 设置检索内容统一变量名为searchQuery   检索值类型通过serachType来传递 目前只支持lable和value之间的转换 -->
       <!-- <template slot="search">
        <input type="text"  v-model="searchQuery" :searchType="'value'">
      </template> -->
    </LSelect>
    <br/>
    ---------------------------------------------------------------------------------------------------------------
    <h2>路由传参</h2>
    <button class="btn" @click="toTestQuery">ToTestQuery</button>
    <br/>
    <button class="btn" @click="toTestParams">ToTestParams</button>
  </div>
</template>

<script>
import LSelect from '@/components/LSelect.vue'
export default {
  name: 'HomeView',
  components: {
    LSelect
  },
  data () {
    return {
      options: [
        { lable: '2', disabled: false, value: 'TWO' },
        { lable: '3', disabled: false, value: 'THREE' },
        { lable: '4', disabled: false, value: 'FOUR' },
        { lable: '5', disabled: false, value: 'FIVE' },
        { lable: '6', disabled: false, value: 'SIX' }
      ],
      // 选中默认值，暂时允许lable值
      // value: [4, 5],
      // value: { lable: '2', disabled: false, value: 'TWO' },
      // value: '4',
      // value: 5,
      value: '',
      getValue: null,
      searchQuery: ''
    }
  },
  methods: {
    input (val) {
      // console.log(val)
    },
    change (val) {
      // console.log(val)
    },
    toTestQuery () {
      this.$router.push({
        path: '/test',
        query: { isQuery: true }
      })
    },
    toTestParams () {
      this.$router.push({
        name: 'test',
        params: { isParams: true }
      })
    }
  }
}
</script>
<style lang="scss">
  $size:'big';
@function btnSize($size) {
  @if $size == 'big' {
    @return (width: 150px, height: 50px);
  } @else if $size == 'small' {
    @return (width: 75px, height: 25px);
  } @else if $size == 'default' {
    @return (width: 100px, height: 35px);
  }
}
.btn {
  $value:btnSize($size);
  width: map-get($map: $value, $key: 'width');
  height: map-get($map: $value, $key: 'height');
}
</style>
