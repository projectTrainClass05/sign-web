<template>
    <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px" class="demo-ruleForm login-container">
        <h3 class="title">系统登录</h3>
        <el-form-item prop="account">
            <el-input type="text" v-model="ruleForm2.account" auto-complete="off" placeholder="账号"></el-input>
        </el-form-item>
        <el-form-item prop="checkPass">
            <el-input type="password" v-model="ruleForm2.checkPass" auto-complete="off" placeholder="密码"></el-input>
        </el-form-item>
        <el-form-item prop="name">
            <el-input type="text" v-model="ruleForm2.name" auto-complete="off" placeholder="姓名"></el-input>
        </el-form-item>
        <el-form-item prop="identity">
            <el-input type="text" v-model="ruleForm2.identity" auto-complete="off" placeholder="身份证"></el-input>
        </el-form-item>
        <el-form-item style="width:100%;">
            <el-button type="primary" style="width:100%;" @click.native.prevent="regi" :loading="logining">注册</el-button>
            <!--<el-button @click.native.prevent="handleReset2">重置</el-button>-->
        </el-form-item>
    </el-form>
</template>

<script>
    import axios from 'axios'
    export default {
        data() {
            return {
                logining: false,
                ruleForm2: {
                    account: '',
                    checkPass: '',
                    name:'',
                    identity:''
                },
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
            regi(){

                var _this = this;
                var url = global.baseurl + 'register';
                var loginParams = {
                    username: this.ruleForm2.account,
                    password: this.ruleForm2.checkPass,
                    name: this.ruleForm2.name,
                    identity: this.ruleForm2.identity
                };
                axios.post(url,{
                    'id':loginParams.username,
                    'password': loginParams.password,
                    'name':loginParams.name,
                    'identity':loginParams.identity
                })
                    .then(function (response){
                        console.log(response.data);
                        if(response.data.flag){
                            _this.$router.push({path: '/login'});
                        }
                        else{
                            alert('注册异常！');
                            _this.logining = false;
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