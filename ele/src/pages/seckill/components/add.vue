<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加秒杀活动' : '编辑秒杀活动'"
      :visible.sync="info.isshow"
      @close="close"
      @opened="open"
    >
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="活动名称">
          <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="活动时限">
          <el-date-picker
            v-model="value"
            type="datetimerange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            @change="conTime"
          >
          </el-date-picker>
        </el-form-item>
        <el-form-item label="一级分类">
          <el-select v-model="form.first_cateid" @change="changeFirst">
            <el-option label="请选择" value="" disabled></el-option>
            <el-option
              v-for="item in list"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级分类">
          <el-select v-model="form.second_cateid" @change="changeSecond">
            <el-option label="请选择" value="" disabled></el-option>
            <el-option
              v-for="item in secondCateList"
              :key="item.id"
              :label="item.catename"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="商品">
          <el-select v-model="form.goodsid">
            <el-option label="请选择" value="" disabled></el-option>
            <el-option
              v-for="item in thirdCateList"
              :key="item.id"
              :label="item.goodsname"
              :value="item.id"
            ></el-option>
          </el-select>
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
  reqSeckillAdd,
  reqSeckillDetail,
  reqSeckillUpdate,
  reqSeckillList,
  reqCateList,
  reqGoodsList,
  reqSpecsDel,
} from "../../../utils/request";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      value: [],
      form: {
        title: "",
        begintime: "",
        endtime: "",
        first_cateid: "",
        second_cateid: "",
        goodsid: "",
        status: "1",
      },
      secondCateList: [],
      thirdCateList: [],
    };
  },
  computed: {
    ...mapGetters({
      list: "cate/list",
    }),
  },
  methods: {
    ...mapActions({
      reqListAction: "seckill/reqListAction",
      reqCateList: "cate/reqListAction",
    }),
    //获取时间并转成时间戳
    conTime() {
      this.form.begintime = this.value[0].getTime();
      this.form.endtime = this.value[1].getTime();
      console.log(this.form.begintime, this.form.endtime);
      console.log(this.value);
    },
    //时间戳转回时间
    // getLocalTime() {
    //   var a = new Date(this.form.endtime);
    //   console.log(a);
    // },

    //一级分类修改了，获取二级分类的list
    changeFirst() {
      //一级分类变了，二级分类的值应该置空
      this.form.second_cateid = "";
      this.getSecondList();
    },
    //获得二级分类list
    getSecondList() {
      reqCateList({ pid: this.form.first_cateid }).then((res) => {
        //二级分类list
        this.secondCateList = res.data.list;
      });
    },

    //一级分类修改了，获取二级分类的list
    changeSecond() {
      //一级分类变了，二级分类的值应该置空
      this.form.goodsid = "";
      this.getGoodsList();
    },
    //获得二级分类list
    getGoodsList() {
      reqGoodsList({ fid: this.form.second_cateid }).then((res) => {
        //二级分类list
        // 如果list某一项的second_cateid == this.form.second_cateid 就把这一项拿出来
        this.thirdCateList = res.data.list.filter(
          (item) => item.second_cateid == this.form.second_cateid
        );
      });
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
      reqSeckillAdd(this.form).then((res) => {
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
    changePid() {
      if (this.form.pid == 0) {
        this.form.type = 1;
      } else {
        this.form.type = 2;
      }
    },
    empty() {
      this.form = {
        title: "",
        begintime: "",
        endtime: "",
        first_cateid: "",
        second_cateid: "",
        goodsid: "",
        status: "1",
      };
      this.secondCateList = [];
      this.thirdCateList = [];
      this.value=[]
    },
    //获取菜单详情 （1条）
    look(id) {
      //发请求
      reqSeckillDetail(id).then((res) => {
        if (res.data.code == 200) {
          //这个时候form是没有id的
          this.form = res.data.list;
          this.form.id = id;
          this.value=[new Date(parseInt(this.form.begintime)), new Date(parseInt(this.form.endtime))]
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    update() {
      reqSeckillUpdate(this.form).then((res) => {
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
    if (this.list.length == 0) {
      this.reqCateList();
    }
  },
};
</script>
<style scoped>
</style>