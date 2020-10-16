<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加轮播图' : '编辑轮播图'"
      :visible.sync="info.isshow"
      @close="close"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="分类名称">
          <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="图片">
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
  reqBannerAdd,
  reqBannerDetail,
  reqBannerUpdate,
} from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      imgUrl: "",
      form: {
        title: "",
        img: null,
        status: 1,
      },
    };
  },
  computed: {
    ...mapGetters({
      list: "banner/list",
    }),
  },
  methods: {
    ...mapActions({
      reqListAction: "banner/reqListAction",
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
      reqBannerAdd(this.form).then((res) => {
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
        title: "",
        img: null,
        status: 1,
      };
      this.imgUrl = "";
    },
    //获取菜单详情 （1条）
    look(id) {
      //发请求
      reqBannerDetail(id).then((res) => {
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
      reqBannerUpdate(this.form).then((res) => {
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