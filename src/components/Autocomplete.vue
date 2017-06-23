<template>
    <div class="quote-watch-autocomplete">
        <input ref="input" type="text" placeholder="简称/代码/拼音" autocomplete="off"
             v-model="keyword"
             @click="focusInput"
             @focus="focusInput"
             @keydown.down="move"
             @keydown.up="move"
             @keydown.enter="select"/>
        <table ref="table" v-show="showResult" :style="position"  @click="select">
            <thead>
                <tr>
                    <th>选项</th>
                    <th width=50>类型</th>
                    <th>代码</th>
                    <th>中文名称</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(r, index) of results" v-bind:class="[selectIdx === index ? 'hover' : '' ]" :data-index="index" @mouseover="mouseoverTable">
                    <td>{{ r.match }}</td>
                    <td>{{ r.type }}</td>
                    <td>{{ r.code }}</td>
                    <td>{{ r.name }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
  name: 'autocomplete',
  data () {
    return {
      position: {},
      showResult: false,
      keyword: '',
      results: [],
      selectIdx: -1
    }
  },
  computed: {
    selectedSecurity: function () {
      if (this.selectIdx > -1 && this.results.length > 0) {
        return this.results.filter((r, i) => {
          return i === this.selectIdx
        })[0]
      } else {
        return {}
      }
    }
  },
  watch: {
    keyword: function (value) {
      if (value === '') {
        this.showResult = false
        return
      }
      this.search('11,41', value, (res) => {
        this._cache[res.id] = res
        this.results = res.data
        this.showResult = true
        this.$nextTick(() => {
          let inputOffsetLeft = this.$refs.input.offsetLeft
          let inputWidth = this.$refs.input.clientWidth
          let tableWidth = this.$refs.table.clientWidth
          this.position = {
            left: (inputOffsetLeft + inputWidth - tableWidth) + 'px'
          }
        })
      })
    }
  },
  methods: {
    select: function () {
      this.showResult = false
      console.log(this.selectedSecurity.code)
    },
    move: function (event) {
      if (this.results.length === 0) {
        return
      }
      let isDown = event.keyCode === 40
      let total = this.results.length
      let idx = this.selectIdx + (isDown ? 1 : -1)
      if (idx < 0 || idx >= total) {
        idx = isDown ? 0 : total - 1
      }
      this.selectIdx = idx
    },
    mouseoverTable: function (event) {
      console.log(event.target)
      // .parent.getAttribute('data-index')
    },
    focusInput: function () {
      if (this.keyword.length > 0) {
        this.showResult = true
      }
    },
    search: function (type, keyword, callback) {
      this._cache = this._cache || {}
      let cacheKey = '//suggest3.sinajs.cn/suggest/type=' + type + '&key=' + keyword + '&name='
      if (this._cache[cacheKey]) {
        callback(this._cache[cacheKey])
      } else {
        let self = this
        let fileRef = document.createElement('script')
        fileRef.setAttribute('type', 'text/javascript')
        fileRef.type = 'text/javascript'
        fileRef.charset = 'gb2312'
        fileRef.__VAR__ = 'suggestdata_' + new Date().getTime()
        fileRef.__CACHEKEY__ = cacheKey
        fileRef.setAttribute('src', cacheKey + fileRef.__VAR__)
        fileRef.onload = fileRef.onreadystatechange = function () {
          if (!this.readyState || this.readyState === 'loaded' || this.readyState === 'complete') {
            let data = window[this.__VAR__]
            if (typeof data !== 'undefined' && data !== '') {
              callback({
                id: this.__CACHEKEY__,
                data: self._handleSearchData(data)
              })
              window[this.__VAR__] = null
            } else {
              callback({
                id: this.__CACHEKEY__
              })
            }
            this.__VAR__ = null
            this.__CACHEKEY__ = null
            document.getElementsByTagName('head')[0].removeChild(fileRef)
          }
        }
        fileRef.onerror = function () {
          callback({
            id: this.__CACHEKEY__
          })
          this.__VAR__ = null
          this.__CACHEKEY__ = null
          document.getElementsByTagName('head')[0].removeChild(fileRef)
        }
        document.getElementsByTagName('head')[0].appendChild(fileRef)
      }
    },
    _handleSearchData: function (data) {
      let ret = []
      data = data.split(';')
      data.forEach(function (d) {
        d = d.split(',')
        ret.push({
          match: d[0],
          type: d[1],
          code: d[2],
          name: d[4]
        })
      })
      return ret
    }
  }
}
</script>

<style scoped>
    input{
        height: 24px;
        margin-right: 16px;
        opacity: 0.8;
        transition: opacity 0.333s cubic-bezier(0, 0, 0.21, 1);
        border: none;
        outline: none;
    }
    table{
        position: absolute;
        width: 320px;
        font-size: 14px;
        color: #666;
        background: #F3F3F3;
        opacity: 0.8;
        margin-top: 2px;
        text-align: center;
        border-collapse: collapse;
        line-height: 18px;
    }
    table tbody tr:hover {
        cursor: pointer;
    }
    table tbody .hover {
        background: #ddd;
    }
</style>
