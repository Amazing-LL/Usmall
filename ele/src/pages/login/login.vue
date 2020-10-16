<template>
  <div class="bg" :style="bg">
    <div class="login_box">
      <h1>登 录</h1>
      <el-form
        :model="form"
        ref="form"
        label-width="100px"
      >
        <el-form-item class="el-form-item" prop="name">
          <el-input
            class="input_box"
            placeholder="请输入用户名"
            v-model="form.username"
            clearable
          >
          </el-input>
        </el-form-item>
        <el-form-item class="el-form-item" prop="password">
          <el-input
            class="input_box"
            placeholder="请输入密码"
            v-model="form.password"
            show-password
          ></el-input>
        </el-form-item>
        <el-button class="el-button" type="primary" plain @click="login">登录</el-button>
      </el-form>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import {reqLogin} from "../../utils/request"
import {successAlert,warningAlert} from "../../utils/alert"
export default {
  data() {
    return {
      //设置背景图
      bg: {
        backgroundImage: "url(" + require("../../assets/images/bg.jpg") + ")",
        backgroundRepeat: "no-repeat",
      },
      form: {
        username: "",
        password:"",
      },
    };
  },
  computed: {
    ...mapGetters({}),
  },
  methods:{
     ...mapActions({
      changeUserInfoAction:"changeUserInfoAction"
    }),
    //登录
    login(){
      reqLogin(this.form).then(res=>{
        if(res.data.code==200){
          //登录成功
          successAlert('登录成功')
          // res.data.list  用户信息 存起来，供很多组件使用
          this.changeUserInfoAction(res.data.list)
          this.$router.push("/")
          // 跳转页面
        }else{
          warningAlert(res.data.msg)
        }
      })
    }
  }
};
</script>

<style>
.bg {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login_box {
  width: 40%;
  height: 50%;
  background-color: #fff;
  border-radius: 1rem;
  text-align: center;
}
h1 {
  font-size: 4rem;
  margin-top: 1rem;
}
.input_box {
  width: 60%;
  margin-top: 2rem;
}
.el-button {
  width: 60%;
  margin-top: 2rem;
}
.el-form-item__content {
  margin-left: 0 !important;
}
.el-form-item__error {
  left: 20%;
}
</style>