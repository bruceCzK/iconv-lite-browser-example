<template>
  <div id="app">
    <div class="container">
      <div class="form-group">
        <label for="charset">Charset</label>
        <input class="form-control" placeholder="charset" id="charset" v-model="charset">
      </div>
      <div class="custom-file">
        <input type="file" class="custom-file-input" id="customFile" @change="iconvDecode">
        <label class="custom-file-label" for="customFile">Choose file</label>
      </div>
      <div class="result">{{result || 'Decode result here...'}}</div>
    </div>
  </div>
</template>

<script>
import iconv from 'iconv-lite'
import toBuffer from 'blob-to-buffer'

export default {
  name: 'App',
  data() {
    return {
      charset: sessionStorage.charset || 'utf8',
      result: ''
    }
  },
  methods: {
    async iconvDecode(e) {
      try {
        if (!e.target.files) {
          return
        }
        const file = e.target.files[0]
        this.result = ''
        const buf = await this.readFile(file)
        sessionStorage.charset = this.charset
        this.result = iconv.decode(buf, this.charset)
        e.target.value = null
      } catch (err) {
        console.error(err)
      }
    },
    async readFile(file) {
      return new Promise((resolve, reject) => {
        toBuffer(file, (err, buf) => {
          if (err) {
            return reject(err)
          }
          resolve(buf)
        })
      })
    }
  }
}
</script>

<style>
  #app {
    margin-left: auto;
    margin-right: auto;
    padding: 1rem;
    max-width: 640px;
  }

  .result {
    margin-top: 1rem;
    padding: 1rem;
    word-break: break-all;
    border-radius: 4px;
    background-color: #f2f2f2;
  }
</style>
