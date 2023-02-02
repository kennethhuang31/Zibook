<template>
  <div id="wrapper">
    <div>
      <div class="top">
        <div class="search">
          <a-input-search
            v-model="urlKeyword"
            size="large"
            placeholder="What do you want to search ?"
            allow-clear
            @search="onSearch"
          />
        </div>
        <h2
          style="
          text-align: center;
          margin: 1rem;
          font-size: 3rem;
          font-weight: 600;
          position: absolute;
          right: 2rem;
          top: 0;
          font-style: italic;
          font-family: -webkit-body, serif;
          width: 17rem;
          "
        >{{ ziDict[selectedWord] }}</h2>
      </div>
      <div
        style="
        width: 17rem;
        height: 19rem;
        float: right;
        margin: 0 2rem 2rem 2rem;
        "
      >
        <img v-if="selectedWord && selectedWord !== lastSelectedWord" :src="'http://www.hanzi5.com/assets/bishun/animation/' + selectedWord + '-bishun.gif'" :alt="selectedWord">
      </div>
      <div style="height: 80vh; overflow-y: auto;" @click="onClick($event)" @mouseup="onSelection" v-html="htmlCode" />
    </div>
  </div>
</template>

<script>
const ziDict = require('../../assets/dict-zi.json')
export default {
  name: 'HomePage',
  data: function() {
    return {
      urlKeyword: 'https://ctext.org/analects/ba-yi/zh',
      selectedWord: '',
      lastSelectedWord: '',
      htmlCode: '',
      ziDict: ''
    }
  },
  mounted() {
    this.ziDict = ziDict
    const urlKeyword = localStorage.getItem('urlKeyword')
    if (urlKeyword) {
      this.urlKeyword = urlKeyword
    }
    this.onSearch()
  },
  methods: {
    onSearch() {
      this.$http.get(this.urlKeyword).then(res => {
        this.htmlCode = res.data
        localStorage.setItem('urlKeyword', this.urlKeyword)
      })
    },
    onSelection: function() {
      const selectedPart = window.getSelection().toString()
      if (selectedPart) {
        this.lastSelectedWord = this.selectedWord
        this.selectedWord = selectedPart.charCodeAt(0).toString(16)
      }
    },
    onClick(e) {
      if (e.path[0].href) {
        this.urlKeyword = e.path[0].href
        this.onSearch()
      }
      e.stopPropagation()
      e.preventDefault()
    }
  }
}
</script>

<style>
  #wrapper {
    padding: 30px;
  }

  .search {
    margin-bottom: 30px;
    width: 100%;
  }

  .top {
    width: 50%;
    display: flex;
    justify-content: space-between;
  }
</style>
