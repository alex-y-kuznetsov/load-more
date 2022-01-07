<template>
  <div v-if="isInit" class="quotes-cover">
    <div class="quotes" ref="quotesContainer" @wheel="loadMore($event)">
      <ul class="quotes-list" ref="quotesList">
        <li 
          class="quotes-item"
          v-for="quote in quotes"
          :key="quote._id"
        >
          <div class="quote-text">{{ quote.quoteText }}</div>
          <div class="quote-author">{{ quote.quoteAuthor }}</div>
        </li>
      </ul>
      <div class="loader" v-if="isLoading">
        <div class="loader-text">Loading quotes...</div>
      </div>
    </div>
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
      initialLoad: true,
      isLoading: false,
      savedScroll: null,
      scrollGap: 60
    }
  },
  methods: {
    init() {
      this.getData();
    },
    getData() {
      this.isLoading = true;
      let dataParams = {
        page: this.page,
        limit: this.limit
      }
      axios.get('https://quote-garden.herokuapp.com/api/v3/quotes', { 
        params: dataParams
      }).then((response) => {
        this.quotes = [...response.data.data, ...this.quotes];
        this.isInit = true;
        this.isLoading = false;
      }).then(() => {
        if (this.initialLoad) {
          this.scrollToBottom();
          this.initialLoad = false;
        }
        if (this.savedScroll) {
          this.loadScrollPosition();
        }
      });
    },
    scrollToBottom() {
      const quotesContainer = this.$refs.quotesContainer;
      quotesContainer.scrollTop = quotesContainer.scrollHeight;
    },
    saveScrollPosition() {
      const quotesContainer = this.$refs.quotesContainer;
      this.savedScroll = quotesContainer.scrollHeight - quotesContainer.scrollTop;
    },
    loadScrollPosition() {
      const quotesContainer = this.$refs.quotesContainer;
      quotesContainer.scrollTop = quotesContainer.scrollHeight - this.savedScroll;
    },
    loadMore(event) {
      const quotesContainerTop = this.$refs.quotesContainer.getBoundingClientRect().top;
      const quotesListTop = this.$refs.quotesList.getBoundingClientRect().top;
      if (event.deltaY < 0 && quotesContainerTop < (quotesListTop + this.scrollGap) && !this.isLoading) {
        this.saveScrollPosition();
        this.page++;
        this.getData();
      }
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
  border: 1px solid #000000;
  padding: 10px;
  box-sizing: border-box;
  overflow: auto;
  position: relative;
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

.loader {
  display: flex;
  flex-direction: column;
  position: absolute;
  background-color: rgba(0, 0, 0, 0.5);
  color: #ffffff;
  height: 100%;
  width: 100%;
  top: 0;
  left: 0;
}

.loader-text {
  margin: auto;
}
</style>
