<template>
  <div>
    <!-- 表格 -->
    <v-list @edit="edit"></v-list>

    <!-- 添加组件 -->
    <v-add :info="info" ref="add"></v-add>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import vAdd from "./components/add";
import vList from "./components/list";
export default {
  props: [],
  components: {
    vList,
    vAdd,
  },
  data() {
    return {
      info: {
        isshow: false,
        isAdd: true,
      },
    };
  },
  computed: {
    ...mapGetters({}),
  },
  methods: {
    ...mapActions({}),
    willAdd() {
      this.info.isshow = true;
      this.info.isAdd = true;
    },
    //编辑
    edit(uid) {
      this.info.isshow = true;
      this.info.isAdd = false;
      //让add组件发起获取详情的请求
      this.$refs.add.look(uid);
    },
  },
  mounted() {},
};
</script>
<style scoped>
</style>