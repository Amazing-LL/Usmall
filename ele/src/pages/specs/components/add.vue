<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加商品规格' : '编辑商品规格'"
      :visible.sync="info.isshow"
      @close="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="规格名称">
          <el-input v-model="form.specsname"></el-input>
        </el-form-item>

        <el-form-item
          label="规格属性"
          v-for="(item, index) in attrArr"
          :key="index"
        >
          <div class="con">
            <div class="input-wrap">
              <el-input v-model="item.value"></el-input>
            </div>
            <el-button type="primary" v-if="index == 0" @click="addAttr"
              >新增规格属性</el-button
            >
            <el-button type="danger" v-else @click="del(index)">删除</el-button>
          </div>
        </el-form-item>

        <el-form-item label="状态">
          <el-switch
            v-model="form.status"
            :active-value="1"
            :inactive-value="2"
          ></el-switch>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" @click="add" v-if="info.isAdd"
          >添 加</el-button
        >
        <el-button type="primary" @click="update" v-else>修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { successAlert, warningAlert } from "../../../utils/alert";
import {
  reqSpecsAdd,
  reqSpecsDetail,
  reqSpecsUpdate,
} from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      attrArr: [{ value: "" }, { value: "" }, { value: "" }],
      form: {
        specsname: "",
        attrs: "",
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({}),
  },
  methods: {
    ...mapActions({
      reqListAction: "specs/reqListAction",
      reqTotalAction: "specs/reqTotalAction",
    }),
    addAttr() {
      this.attrArr.push({ value: "" });
    },
    del(index) {
      this.attrArr.splice(index, 1);
    },
    cancel() {
      this.info.isshow = false;
    },
    close() {
      if (!this.info.isAdd) {
        this.empty();
      }
    },
    add() {
      
      this.form.attrs = JSON.stringify(this.attrArr.map((item) => item.value));
      // console.log(this.attrArr.map((item) => item.value));
      // console.log(this.form.attrs);
      reqSpecsAdd(this.form).then((res) => {
        if (res.data.code == 200) {
          //成功
          successAlert(res.data.msg);
          //弹框消失
          this.cancel();
          //列表置空
          this.empty();
          //list数据要刷新
          this.reqListAction();
          this.reqTotalAction();
        } else {
          successAlert(res.data.msg);
        }
      });
    },
    empty() {
      this.form = {
        specsname: "",
        attrs: "",
        status: 1,
      };
    },
    //获取菜单详情 （1条）
    look(id) {
      //发请求
      reqSpecsDetail(id).then((res) => {
        if (res.data.code == 200) {
          //这个时候form是没有id的
          this.form = res.data.list[0];
          this.attrArr = JSON.parse(this.form.attrs).map((item) => ({value: item,}));
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    update() {
      reqSpecsUpdate(this.form).then((res) => {
        if (res.data.code == 200) {
          successAlert(res.data.msg);
          this.empty();
          this.cancel();
          this.reqListAction();
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
  },
  mounted() {
  },
};
</script>
<style scoped>
.con {
  display: flex;
}
.con input-wrap {
  flex: 1;
}
</style>