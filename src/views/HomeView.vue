<template>
  <div class="home">
    <LSelect :options="options" :value="value" :multiple="false" :searchable="true
    " @change="change" @remove="input" @menuOpen="input" @search="input"></LSelect>
  </div>
</template>

<script>
// @ is an alias to /src
import LSelect from '@/components/LSelect.vue'
export default {
  name: 'HomeView',
  components: {
    LSelect
  },
  data () {
    return {
      options: [
        { text: '1' },
        { text: '2' },
        { text: '3' },
        { text: '4' },
        { text: '5' },
        { text: '6' }
      ],
      value: '',
      getValue: null
    }
  },
  methods: {
    input (val) {
      console.log(val)
    },
    change (val) {
      console.log(val)
    }
  }
}
</script>
