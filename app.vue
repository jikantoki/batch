<template lang="pug">
.div
  h1.title Hello!Batch
  .buttons
    button(
      v-show="!doing"
      @click="doing=true;fetching()"
      ) Do
    button(
      v-show="doing"
      @click="doing=false"
      ) Stop
  .main
    h1 OK
    .res
      p(v-for="url, key in fetchList" :key="key")
        a(:href="url" target="_blank") {{ url }}
    h1 Error
    .error
      p(v-for="url, key in errorList" :key="key") {{ url }}
</template>

<script>
export default {
  data() {
    return {
      fetchList: [],
      errorList: [],
      doing: false,
    }
  },
  watch: {
    errorList: {
      handler(newList) {
        if (newList.length > 10)
          this.errorList = newList.slice(newList.length - 10, newList.length)
      },
      deep: true,
    },
  },
  methods: {
    async fetching() {
      const deadline = new Date()
      deadline.setDate(deadline.getDate() + Math.floor(Math.random() * 5))
      const date = `${String(deadline.getMonth() + 1).padStart(2, '0')}${String(
        deadline.getDate()
      ).padStart(2, '0')}`
      const domainList = [9, 28, 38]
      const domainListIndex = Math.floor(Math.random() * domainList.length)
      const domain = domainList[domainListIndex]
      const url = `https://${domain}.gigafile.nu/${date}-${this.randomString()}`
      const res = await fetch(url)
      if (res) {
        const redirected = res.redirected
        const statusCode = res.status
        if (!redirected && statusCode === 200) {
          this.fetchList.push(url)
        } else {
          this.errorList.push(url)
        }
      }
      if (this.doing) {
        this.fetching()
      }
    },
    randomChar() {
      const str = '0123456789abcdef'
      const strArray = str.split('')
      const index = Math.floor(Math.random() * strArray.length)
      return strArray[index]
    },
    randomString(length = 33) {
      let string = ''
      for (let count = 0; count < length; count++) {
        string += this.randomChar()
      }
      return string
    },
  },
}
</script>

<style lang="scss"></style>
