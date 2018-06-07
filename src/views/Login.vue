<template>
  <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px" class="demo-ruleForm login-container">
    <h3 class="title">系统登录</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="ruleForm2.account" auto-complete="off" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="ruleForm2.checkPass" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <el-checkbox v-model="checked" checked class="remember">记住密码</el-checkbox>
    <el-form-item style="width:100%;">
      <el-button type="primary" style="width:100%;" @click.native.prevent="handleSubmit2" :loading="logining">登录</el-button>
      <!--<el-button @click.native.prevent="handleReset2">重置</el-button>-->
    </el-form-item>
    <el-form-item style="width:100%;">
      <el-button type="primary" style="width:100%;" @click.native.prevent="toRegister" :loading="logining2">注册</el-button>
      <!--<el-button @click.native.prevent="handleReset2">重置</el-button>-->
    </el-form-item>
    <!--<div id="allmap">-->
      <!--<el-button @click.native.prevent="get"></el-button>-->
    <!--</div>-->
  </el-form>
</template>

<script>
  import { requestLogin } from '../api/api';
  import axios from 'axios'
  import ElButton from "../../node_modules/element-ui/packages/button/src/button.vue";
  //import NProgress from 'nprogress'
  export default {
      components: {ElButton},
      data() {
      return {
        logining: false,
        logining2:false,
        ruleForm2: {
          account: '',
          checkPass: ''
        },
        loginStatus:false,
        rules2: {
          account: [
            { required: true, message: '请输入账号', trigger: 'blur' },
            //{ validator: validaePass }
          ],
          checkPass: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            //{ validator: validaePass2 }
          ]
        },
        checked: true
      };
    },

    methods: {
      handleReset2() {
        this.$refs.ruleForm2.resetFields();
      },
      handleSubmit2(ev) {
        var _this = this;
        this.$refs.ruleForm2.validate((valid) => {
          if (valid) {
            //_this.$router.replace('/table');
            this.logining = true;
            //NProgress.start();
            var loginParams = {
                username: this.ruleForm2.account,
                password: this.ruleForm2.checkPass
            };
            var url = global.baseurl + 'user';
            console.log(url);
            //http://47.106.173.130/api/v1/user
            axios.post(url,{ 'id':loginParams.username,'password': loginParams.password})
                .then(function (response){
                    console.log(response.data);
                    sessionStorage.setItem('user', JSON.stringify(loginParams));
                    sessionStorage.setItem('token', (response.data.token));
                    if(response.data.flag){
                        _this.$router.push({path: '/table'});
                    }
                    else{
                        alert('用户名或密码错误！');
                        _this.logining = false;
                    }
                });
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },

      toRegister(){
          this.$router.push({ path: '/register' });
      },
      setlogin(){
          this.loginStatus = true;
      },

        get(){
            var map = new BMap.Map("allmap");
            var point = new BMap.Point(116.331398,39.897445);
            map.centerAndZoom(point,12);

            var geolocation = new BMap.Geolocation();
            geolocation.getCurrentPosition(function(r){
                if(this.getStatus() == BMAP_STATUS_SUCCESS){
                    var mk = new BMap.Marker(r.point);
                    map.addOverlay(mk);
                    map.panTo(r.point);
                    alert('您的位置：'+r.point.lng+','+r.point.lat);
                }
                else {
                    alert('failed'+this.getStatus());
                }
            });
        }
    }
  }

</script>

<style lang="scss" scoped>
  .login-container {
    /*box-shadow: 0 0px 8px 0 rgba(0, 0, 0, 0.06), 0 1px 0px 0 rgba(0, 0, 0, 0.02);*/
    -webkit-border-radius: 5px;
    border-radius: 5px;
    -moz-border-radius: 5px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
    .title {
      margin: 0px auto 40px auto;
      text-align: center;
      color: #505458;
    }
    .remember {
      margin: 0px 0px 35px 0px;
    }
  }
</style>