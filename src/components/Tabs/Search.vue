<template>
  <div>
    <div class="search">
      <img class="find" src="../../assets/find.svg" alt="find">
      <x-input
        placeholder="搜索歌曲、歌手、专辑"
        v-model="searchText"
        @on-enter="search"
        @on-click-clear-icon="clear"
        @on-change="change"
      ></x-input>
    </div>
    <SearchHot v-if="isWord" @hot-search="search"></SearchHot>
    <div v-else>
      <section v-if="error">
        <h1>暂无搜索结果</h1>
      </section>
      <section v-else>
        <div v-if="loading">Loading.....</div>
        <Song v-else v-for="item in searchResult" :key="item.id" :music="item"></Song>
      </section>
    </div>
  </div>
</template>

<script>
import { XInput } from "vux";
import SearchHot from "@/components/SearchHot.vue";
import Song from "@/components/Song.vue";
import { mapActions, mapGetters } from "vuex";

export default {
  data() {
    return {
      searchText: "",
      isWord: false,
      loading: true,
      error: false
    }
  },
  components: {
    XInput,
    SearchHot,
    Song
  },
  computed: {
    ...mapGetters(["searchResult"])
  },
  methods: {
    ...mapActions(["getSearch", "hotWord"]),
    search: function(value) {
      this.isWord = false;
      this.getSearch(value)
        .then(() => {
          this.searchText = value;
          this.loading = false;
        })
        .catch(() => {
          this.searchText = value;
          this.error = true;
        });
    },
    clear: function() {
      this.isWord = true;
    },
    change: function(value) {
      if (value === "") {
        this.isWord = true;
      }
    }
  },
  created() {
    this.hotWord().then(() => {
      this.isWord = true;
    });
  }
}
</script>

<style scoped>
.vux-x-input {
  background: #ebecec;
  border-radius: 30px;
  padding: 0 30px;
  height: 30px;
  width: 100%;
}

.weui-cell:before {
  border: none;
}

.search {
  position: relative;
  background: #fbfcfd;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  padding: 0 10px;
}

.find {
  position: absolute;
  left: 11px;
  top: 24px;
  margin: 0 8px;
  vertical-align: middle;
  display: inline-block;
  width: 13px;
  height: 13px;
  z-index: 99;
}
</style>
