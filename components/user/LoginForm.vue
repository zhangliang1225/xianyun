<template>
    <el-form 
        :model="form" 
        ref="form"
        :rules="rules" 
        class="form">
  <!-- 用户名 -->
        <el-form-item class="form-item" prop="username">
            <el-input 
            v-model="form.username"
            placeholder="用户名/手机">
            </el-input>
        </el-form-item>
<!-- 密码 -->
        <el-form-item class="form-item" prop="password">
            <el-input 
            v-model="form.password"
            placeholder="密码" 
            type="password">
            </el-input>
        </el-form-item>

        <p class="form-text">
            <nuxt-link to="#">忘记密码</nuxt-link>
        </p>
 <!-- 提交按钮 -->
        <el-button 
        class="submit"
        type="primary"
        @click="handleLoginSubmit">
            登录
        </el-button>
    </el-form>

</template>

<script>
export default {
    data(){
        return {
            // 表单数据
            form: {
                username : '',
                password : ''
            },
            // 表单规则
            rules: {
                username : [
                      { required: true, message: '请输入用户名', trigger: 'blur' },
                ],
                password : [
                      { required: true, message: '请输入密码', trigger: 'blur' },
                ]
            },
        }
    },
    methods: {
        // 提交登录
       handleLoginSubmit(){
        //    console.log(this.form)
    //  表单验证成功的操作
         this.$refs.form.validate((valid) => {
          if (valid) {
             console.log(this.form)
            // this.$axios({
            //     url : '/accounts/login',
            //     method : 'post',
            //     data : this.form
            // }).then( res =>{
            //     this.$store.commit('user/getData',res.data)
            // })
            this.$store.dispatch('user/login',this.form).then(()=>{
                this.$message.success('登录成功！')
                this.$router.push('/')
            })
            
          } else {
            this.$message.warning('温馨提示，登录验证失败！')
          }
        });
    }
  }
}
</script>

<style scoped lang="less">
    .form{
        padding:25px;
    }

    .form-item{
        margin-bottom:20px;
    }

    .form-text{
        font-size:12px;
        color:#409EFF;
        text-align: right;
        line-height: 1;
    }

    .submit{
        width:100%;
        margin-top:10px;
    }
</style>