<template>
  <div class="container grid-lg my-2 py-2">
    <div class="card mb-3" v-if="listenQuotes.length">
      <div class="card-header">
        <div class="h4">Acompanhando</div>
      </div>
      <div class="card-body">
        <watch-list-quotes :listenQuotes="listenQuotes" @unListen="unListen" />
      </div>
    </div>
    <div class="card">
      <div class="card-header">
        <div class="h4">Todas das Moedas</div>
        <div class="card-body">
          <list-quotes
            :quotes="quotes"
            :listenQuotes="listenQuotes"
            @listen="onListen"
            @unListen="unListen"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, reactive, toRefs } from "vue";
import api from "./services/api";
import ListQuotes from "./components/ListQuotes.vue";
import WatchListQuotes from "./components/WatchListQuotes.vue";

export default {
  name: "App",
  components: {
    ListQuotes,
    WatchListQuotes,
  },
  setup() {
    const data = reactive({
      quotes: {},
      listenQuotes: [],
    });

    onMounted(async () => {
      const response = await api.all();
      data.quotes = response.data;
    });

    function onListen(code) {
      data.listenQuotes.push(code);
    }

    function unListen(code) {
      const index = data.listenQuotes.indexOf(code);
      data.listenQuotes.splice(index, 1);
    }

    return {
      ...toRefs(data),
      onListen,
      unListen,
    };
  },
};
</script>

