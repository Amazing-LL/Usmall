<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加会员' : '编辑会员'"
      :visible.sync="info.isshow"
      @close="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="手机号">
          <el-input v-model="form.phone"></el-input>
        </el-form-item>

        <el-form-item label="昵称">
          <el-input v-model="form.nickname"></el-input>
        </el-form-item>

        <el-form-item label="密码">
          <el-input v-model="form.password"></el-input>
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
        <el-button type="primary" @click="update">修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { successAlert, warningAlert } from "../../../utils/alert";
import { reqMemberDetail, reqMemberUpdate } from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      form: {
        phone: "",
        nickname: "",
        password: "",
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({
      memberList: "member/list",
    }),
  },
  methods: {
    ...mapActions({
      //请求会员的list
      reqMemberList: "member/reqListAction",
    }),
    cancel() {
      this.info.isshow = false;
    },
    close() {
      if (!this.info.isAdd) {
        this.empty();
      }
    },
    empty() {
      this.form = {
        phone: "",
        nickname: "",
        password: "",
        status: 1,
      };
    },
    //获取菜单详情 （1条）
    look(uid) {
      //发请求
      reqMemberDetail(uid).then((res) => {
        if (res.data.code == 200) {
          //这个时候form是没有id的
          this.form = res.data.list;
          this.form.password = "";
          this.form.uid = uid;
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    update() {
      console.log(this.form);
      if (this.form.password == "") {
        warningAlert("密码不能为空");
        return;
      }
      reqMemberUpdate(this.form).then((res) => {
        if (res.data.code == 200) {
          successAlert(res.data.msg);
          this.empty();
          this.cancel();
          this.reqMemberList();
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
  },
  mounted() {},
};
</script>
<style scoped>
</style>