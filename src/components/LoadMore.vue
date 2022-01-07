<template>
  <div v-if="isInit" class="quotes-cover">
    <div class="quotes" ref="quotesContainer">
      <ul class="quotes-list">
        <li 
          class="quotes-item"
          v-for="quote in quotes"
          v-bind:key="quote._id"
        >
          <div class="quote-text">{{ quote.quoteText }}</div>
          <div class="quote-author">{{ quote.quoteAuthor }}</div>
        </li>
      </ul>
    </div>
    <button class="load-more" @click.stop="loadMore">Load More</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'LoadMore',
  data() {
    return {
      isInit: false,
      quotes: [],
      page: 1,
      limit: 10,
      initialLoad: true
    }
  },
  methods: {
    init() {
      this.getData();
    },
    getData() {
      let dataParams = {
        page: this.page,
        limit: this.limit
      }
      axios.get('https://quote-garden.herokuapp.com/api/v3/quotes', { 
        params: dataParams
      }).then((response) => {
        this.quotes = [...response.data.data, ...this.quotes];
        this.isInit = true;
      }).then(() => {
        if (this.initialLoad) {
          this.scrollToBottom();
        }
        this.initialLoad = false;
      });
    },
    scrollToBottom() {
      let quotesContainer = this.$refs.quotesContainer;
      quotesContainer.scrollTop = quotesContainer.scrollHeight;
    },
    loadMore() {
      this.page++;
      this.getData();
    }
  },
  created() {
    this.init();
  }
}
</script>

<style scoped lang="less">
.quotes-cover {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  height: 100%;
}

.quotes {
  margin: auto;
  width: 600px;
  height: 600px;
  border: 1px solid black;
  padding: 10px;
  box-sizing: border-box;
  overflow: auto;
}

.quotes-list {
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}

.quotes-item {
  text-align: left;
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #cccccc;
  list-style: none;
}

.quote-author {
  text-align: right;
  font-size: 12px;
  color: #cccccc;
}
</style>
