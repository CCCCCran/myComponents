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
        { text: '1', disabled: true },
        { text: '2', disabled: true },
        { text: '3', disabled: true },
        { text: '4', disabled: false },
        { text: '5', disabled: false },
        { text: '6', disabled: false }
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
