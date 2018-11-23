<template>
<div>
<table>
  <tr v-for="(item, $index) in list" :key="$index">
    <td><a :href="item.url">{{item.title}}</a></td>
  </tr>
</table>
<infinite-loading @infinite="infiniteHandler" :distance="10"></infinite-loading>
</div>
</template>

<script>
import InfiniteLoading from 'vue-infinite-loading'
import axios from 'axios'
import Vue from "vue";
Vue.use(InfiniteLoading, {
  system: {
    throttleLimit: 500,
    /* other settings need to configure */
  },
});
const api = 'https://qiita.com/api/v2/items'
export default {
  name: "app",
  components: {
    InfiniteLoading
  },
  data() {
    return {
      page: 1,
      list: [],
    }
  },
  methods: {
    infiniteHandler($state) {
      axios.get(api, {
        params: {
          page: this.page,
          per_page: 20
        },
        headers: {
          'Authorization' : 'Bearer ' + '56b474b63a62df4112ff7d341c8dfc8a24960f50'
        }
      }).then(({ data }) => {
        if (data.length) {
          this.page += 1
          this.list.push(...data)
          $state.loaded()
        } else {
          $state.complete()
        }
      }).catch((err) => {
          $state.complete()
      })
    }
  }
}
</script>
