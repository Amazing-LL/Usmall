<template>
  <div>
    <el-table
      :data="list"
      style="width: 100%; margin-bottom: 20px"
      row-key="id"
      border
      default-expand-all
      :tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
    >
      <el-table-column prop="title" label="活动名称" sortable width="180">
      </el-table-column>
      <el-table-column label="状态">
      <template slot-scope="scope">
        <el-button v-if="scope.row.status == 1" type="primary">启用</el-button>
        <el-button v-else type="info">禁用</el-button>
      </template>
    </el-table-column>
    <el-table-column prop="status" label="操作">
      <template slot-scope="scope">
        <el-button type="primary" @click="edit(scope.row.id)">编辑</el-button>
        <el-button type="danger" @click="del(scope.row.id)">删除</el-button>
      </template>
    </el-table-column>
    </el-table>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import {reqSeckillDel} from "../../../utils/request"
import { successAlert, warningAlert } from "../../../utils/alert";
export default {
  props: [],
  components: {},
  data() {
    return {}
  },
  computed: {
    ...mapGetters({
      list:"seckill/list"
    }),
  },
  methods: {
    ...mapActions({
      reqListAction:"seckill/reqListAction"
    }),
    //编辑
    edit(id){
      this.$emit("edit",id)
    },
    del(id){
      this.$confirm("你确定要删除吗？", "删除提示", {
        confirmButtonText: "删除",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          //点了确定按钮
          reqSeckillDel(id).then(res=>{
            if(res.data.code==200){
              successAlert(res.data.msg)
              this.reqListAction();
            }else{
              warningAlert(res.data.msg)
            }
          })
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    }
  },
  mounted() {
    this.reqListAction()
  },
};
</script>
<style scoped>
</style>
</style>