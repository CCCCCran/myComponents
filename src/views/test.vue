<template>
    <div>
      <button @click="returnHome">返回home</button>
      <br/>
      home传过来的query参数为：{{ this.$route.query  }}
      <br/>
      home传过来的params参数为：{{ this.$route.params  }}
    </div>
</template>
<script>
export default {
  name: 'testComponent',
  data () {
    return {
    }
  },
  methods: {
    returnHome () {
      this.$router.push({
        path: '/'
      })
    }
  }
}
</script>
<style lang="scss">

</style>
