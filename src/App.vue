<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <!-- <custom-table v-bind:data="quotes" /> -->
    <custom-table
      v-bind:data="quotes"
      v-bind:total="count"
      v-bind:page="page"
      v-bind:pageakhir="lastPage"
      v-bind:limit="limit"
      v-on:on-next="handleNext"
      v-on:on-prev="handlePrev"
      v-bind:limitOption="limitOption"
      v-on:on-limit-changed="handleLimitChanged"
      v-on:on-search="handelSearch"
    />
  </div>
</template>

<script>
import CustomTable from "./components/CustomTable.vue";
import request from "./service/request.js";

// let data = {
//   count: 5421,
//   limit: 10,
//   page: 1,
//   pages: 543,
//   lastPage: false,
//   quotes: [
//     {
//       _id: "5e26fcc78f9c0856986fc141",
//       text: "What we see depends mainly on what we look for.",
//       author: "John Lubbock",
//       created: "2020-01-21T13:29:43.639Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc78f9c0856986fc140",
//       text: "Excellence is to do a common thing in an uncommon way.",
//       author: "Booker Washington",
//       created: "2020-01-21T13:29:43.419Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc78f9c0856986fc13f",
//       text:
//         "Sometimes the most important thing in a whole day is the rest we take between two deep breaths.",
//       author: "Etty Hillesum",
//       created: "2020-01-21T13:29:43.202Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc68f9c0856986fc13e",
//       text:
//         "Using the power of decision gives you the capacity to get past any excuse to change any and every part of your life in an instant.",
//       author: "Tony Robbins",
//       created: "2020-01-21T13:29:42.984Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc68f9c0856986fc13d",
//       text:
//         "Here is one quality that one must possess to win, and that is definiteness of purpose, the knowledge of what one wants, and a burning desire to possess it.",
//       author: "Napoleon Hill",
//       created: "2020-01-21T13:29:42.768Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc68f9c0856986fc13c",
//       text: "To be wronged is nothing unless you continue to remember it.",
//       author: "Confucius",
//       created: "2020-01-21T13:29:42.551Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc68f9c0856986fc13b",
//       text:
//         "To know your purpose is to live a life of direction, and in that direction is found peace and tranquillity.",
//       author: "Byron Pulsifer",
//       created: "2020-01-21T13:29:42.328Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc68f9c0856986fc13a",
//       text: "Victory belongs to the most persevering.",
//       author: "Napoleon Bonaparte",
//       created: "2020-01-21T13:29:42.112Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc58f9c0856986fc139",
//       text:
//         "He who experiences the unity of life sees his own Self in all beings, and all beings in his own Self, and looks on everything with an impartial eye.",
//       author: "Buddha",
//       created: "2020-01-21T13:29:41.899Z",
//       __v: 0
//     },
//     {
//       _id: "5e26fcc58f9c0856986fc138",
//       text:
//         "No valid plans for the future can be made by those who have no capacity for living now.",
//       author: "Alan Watts",
//       created: "2020-01-21T13:29:41.679Z",
//       __v: 0
//     }
//   ]
// };

export default {
  name: "app",
  async created() {
    // let res = await request("http://apicollections.herokuapp.com/api/quotes");
    // this.quotes = res.quotes;
    // this.count = res.count;
    // this.limit = res.limit;
    // this.page = res.page;
    // this.lastPage = res.lastPage;
    this.getQuotes();
  },
  methods: {
    async getQuotes(params = "") {
      if (this.q) {
        params += `&q=${this.q}`;
      }

      // let url = "http://apicollections.herokuapp.com/api/quotes?" + params;
      let url = `http://apicollections.herokuapp.com/api/quotes?${params}`;
      let res = await request(url);

      this.quotes = res.quotes;
      this.count = res.count;
      this.limit = res.limit;
      this.page = res.page;
      this.lastPage = res.lastPage;
    },
    hasPrevious() {
      return this.page > 1;
    },
    hasNext() {
      // return this.page * limit < this.count;
      return !this.lastPage; //from api
    },
    handleNext() {
      if (this.hasNext()) {
        //todo: next page
        let params = `page=${this.page + 1}&limit=${this.limit}`;
        this.getQuotes(params);
      }
    },
    handlePrev() {
      if (this.hasPrevious()) {
        //todo: prev page
        let params = `page=${this.page - 1}&limit=${this.limit}`;
        this.getQuotes(params);
      }
    },
    handleLimitChanged(limit) {
      let params = `page=${this.page}&limit=${limit}`;
      this.getQuotes(params);
    },
    handelSearch(searchCriteria) {
      this.q = searchCriteria;
      this.getQuotes(`page=${this.page}&limit=${this.limit}`);
    }
  },
  // data() {
  //   return {
  //     quotes: data.quotes
  //   };
  // },
  data() {
    return {
      quotes: [],
      count: 0,
      limit: 10,
      page: 1,
      lastPage: false,
      limitOption: [10, 15, 25, 50]
    };
  },

  components: {
    CustomTable
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
  margin-bottom: 60px;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
}
</style>
