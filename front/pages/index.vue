<template>
  <v-layout justify-center>
    <v-flex xs12 sm12 md12>
      <!-- グレー背景 -->
      <v-card class="grey lighten-3 mt-4 mb-4 pa-3 ">
        <v-container fluid>
          <v-radio-group v-model="sortKey" row>
            <v-radio label="投稿が新しい順" value="date" />
            <v-radio label="いいね数" value="likes" />
            <v-radio label="おすすめ度" value="rating" />
          </v-radio-group>
          <NewTopicBtn2 v-show="isLoggedIn" />
        </v-container>
        <!-- トピックコンポーネントにソートされたデータを渡す -->
        <AllTopics :topics="TopicOrderBy" />
      </v-card>
      <!-- トピック投稿コンポーネント -->
      <NewTopicBtn v-show="isLoggedIn" />
    </v-flex>
  </v-layout>
</template>

<script>
import NewTopicBtn from "../components/NewTopicBtn.vue"
import NewTopicBtn2 from "../components/NewTopicBtn2.vue"
import AllTopics from "../components/AllTopics.vue"

export default {
  components: {
    NewTopicBtn,
    NewTopicBtn2,
    AllTopics
  },
  // ページ読み込み時にTopicコントローラーにgetリクエスト
  async asyncData({ app }) {
    const res = await app.$axios.$get(process.env.apiBaseUrl + "/api/topics")
    console.log(res)
    return { topics: res }
  },
  data() {
    return {
      comments: false,
      sortKey: "date"
    }
  },
  computed: {
    isLoggedIn() {
      return this.$store.state.auth.isLoggedIn
    },
    // セレクタの値によってソートの切り替え
    TopicOrderBy() {
      if (this.sortKey === "date") {
        return _.orderBy(this.topics, ["created_at"], ["desc"])
      } else if (this.sortKey === "likes") {
        return _.orderBy(this.topics, [`likes`], ["desc"])
      } else if (this.sortKey === "rating") {
        return _.orderBy(this.topics, [`rating`], ["desc"])
      }
    }
  }
}
</script>
