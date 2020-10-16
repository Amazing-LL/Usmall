<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加分类' : '编辑分类'"
      :visible.sync="info.isshow"
      @close="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="上级分类">
          <el-select v-model="form.pid" placeholder="请选择上级分类">
            <el-option label="顶级分类" :value="0"></el-option>
            <el-option
              v-for="item in list"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="分类名称">
          <el-input v-model="form.catename"></el-input>
        </el-form-item>
        <el-form-item label="图片" v-if="form.pid !== 0">
          <div class="img_wrap">
            <h3>+</h3>
            <img v-if="imgUrl" :src="imgUrl" alt="" />
            <input v-if="info.isshow" type="file" @change="getFile" />
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
  reqCateAdd,
  reqCateDetail,
  reqCateUpdate,
} from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      imgUrl: "",
      form: {
        pid: 0,
        catename: "",
        img: null,
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({
      list: "cate/list",
    }),
  },
  methods: {
    ...mapActions({
      reqListAction: "cate/reqListAction",
    }),
    getFile(e) {
      let file = e.target.files[0];

      //1.大小不超过2M 1M=1024KB 1KB=1024B
      if (file.size > 2 * 1024 * 1024) {
        warningAlert("文件不能超过2M");
        return;
      }

      //2.是图片
      let imgExtArr = [".jpg", ".png", ".jpeg", ".gif"];
      let extname = file.name.slice(file.name.lastIndexOf(".")); //'.jpg'
      if (!imgExtArr.some((item) => item == extname)) {
        warningAlert("文件格式不正确");
        return;
      }


      this.imgUrl = URL.createObjectURL(file);
      this.form.img = file;
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
      reqCateAdd(this.form).then((res) => {
        if (res.data.code == 200) {
          //成功
          successAlert(res.data.msg);
          //弹框消失
          this.cancel();
          //列表置空
          this.empty();
          //list数据要刷新
          this.reqListAction();
        } else {
          successAlert(res.data.msg);
        }
      });
    },
    empty() {
      this.form = {
        pid: 0,
        catename: "",
        img: null,
        status: 1,
      };
      this.imgUrl=""
    },
    //获取菜单详情 （1条）
    look(id) {
      //发请求
      reqCateDetail(id).then((res) => {
        if (res.data.code == 200) {
          //这个时候form是没有id的
          this.form = res.data.list;
          this.form.id = id;
          this.imgUrl = this.$imgPre + this.form.img;
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    update() {
      reqCateUpdate(this.form).then((res) => {
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
  mounted() {},
};
</script>
<style scoped>
.img_wrap {
  width: 150px;
  height: 150px;
  border: 1px dashed #ccc;
  position: relative;
}
.img_wrap h3 {
  font-size: 30px;
  font-weight: normal;
  text-align: center;
  line-height: 150px;
}
.img_wrap input {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  cursor: pointer;
}
.img_wrap img {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
</style>