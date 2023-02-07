<template>
  <list-quotes
    :quotes="quotes"
    :listenQuotes="listenQuotes"
    @unListen="unListen"
  />
  <div class="mt-2 text-right">
    <cite class="text-small">
      Atualizará novamente em <b>{{ nextUpdateTime }} segundos</b>
    </cite>
  </div>
</template>

<script>
import api from "@/services/api";
import { ref, reactive, toRefs, onMounted, watch, onUnmounted } from "vue";
import ListQuotes from "./ListQuotes.vue";

const Current_Update_Time = 30;

export default {
  name: "WatchListQuotes",
  components: { ListQuotes },
  emits: ["unListen"],
  setup(props, { emit }) {
    const interval = ref(null);
    const quotes = ref({});
    const nextUpdateTime = ref(30);

    const methods = reactive({
      unListen(code) {
        emit("unListen", code);
      },

      async refresh() {
        const { data } = await api.listen(props.listenQuotes);
        quotes.value = data;
      },

      restartInterval() {
        clearInterval(interval.value);
        nextUpdateTime.value = Current_Update_Time;
        interval.value = setInterval(() => {
          nextUpdateTime.value--;
          if (nextUpdateTime.value === 0) {
            nextUpdateTime.value = Current_Update_Time;
            this.refresh();
          }
        }, 1000);
      },
    });

    onMounted(() => {
      methods.refresh();
      methods.restartInterval();
    });

    onUnmounted(() => {
      clearInterval(interval.value);
    });

    watch(props, () => {
      methods.refresh();
    });

    return {
      ...toRefs(methods),
      quotes,
      nextUpdateTime,
    };
  },
  props: {
    listenQuotes: {
      type: Array,
      required: true,
    },
  },
};
</script>