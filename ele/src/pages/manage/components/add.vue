<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加管理员' : '编辑管理员'"
      :visible.sync="info.isshow"
      @close="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="所属角色">
          <el-select v-model="form.roleid" placeholder="请选择上级菜单">
            <el-option label="请选择" disabled value=""></el-option>
            <el-option
              v-for="item in roleList"
              :key="item.id"
              :label="item.rolename"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
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
  reqManageAdd,
  reqManageDetail,
  reqManageUpdate,
} from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      form: {
        roleid: "",
        username: "",
        password: "",
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({
      roleList: "role/list",
    }),
  },
  methods: {
    ...mapActions({
      //请求角色的list
      reqRoleList: "role/reqListAction",
      //请求管理员list
      reqListAction:"manage/reqListAction",
      reqTotalAction:"manage/reqTotalAction"
    }),
    cancel() {
      this.info.isshow = false;
    },
    close() {
      if (!this.info.isAdd) {
        this.empty();
      }
    },
    add() {
      reqManageAdd(this.form).then((res) => {
        if (res.data.code == 200) {
          //成功
          successAlert(res.data.msg);
          //弹框消失
          this.cancel();
          //列表置空
          this.empty();
          //list数据要刷新
          this.reqListAction();
          this.reqTotalAction()
        } else {
          successAlert(res.data.msg);
        }
      });
    },
    empty() {
      this.form = {
        roleid: "",
        username: "",
        password: "",
        status: 1,
      };
    },
    //获取菜单详情 （1条）
    look(uid) {
      //发请求
      reqManageDetail(uid).then((res) => {
        if (res.data.code == 200) {
          //这个时候form是没有id的
          this.form = res.data.list;
          this.form.password="";
          // this.form.uid=uid
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    update() {
      console.log(this.form);
      reqManageUpdate(this.form).then((res) => {
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
    if (this.roleList.length == 0) {
      this.reqRoleList();
    }
    this.reqListAction()
  },
};
</script>
<style scoped>
</style>