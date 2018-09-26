<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="brand-info">
          <h2 class="brand-info__text">renren-fast-vue</h2>
          <p class="brand-info__intro">renren-fast-vue基于vue、element-ui构建开发，实现renren-fast后台管理前端功能，提供一套更优的前端解决方案。</p>
        </div>
        <div class="login-main">
          <h3 class="login-title">管理员登录</h3>
          <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
                   status-icon>
            <el-form-item prop="loginName">
              <el-input v-model="dataForm.loginName" placeholder="帐号"></el-input>
            </el-form-item>
            <el-form-item prop="password">
              <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
            </el-form-item>

          </el-form>

            <!--<el-button type="primary" @click="modifyPassword()">修改密码</el-button>-->
            <!--<el-button type="primary" @click="getUserList()">获取用户列表</el-button>-->
            <!--<el-button type="primary" @click="addUser()">添加用户信息</el-button>-->
            <!--<el-button type="primary" @click="editUser()">编辑用户信息</el-button>-->
            <!--<el-button type="primary" @click="resetUserInfo()">重置用户的密码</el-button>-->
            <!--<el-button type="primary" @click="deleteUser()">删除</el-button>-->
            <!--<el-button type="primary" @click="enableUser()">启用</el-button>-->
            <!--<el-button type="primary" @click="disableUser()">禁用</el-button>-->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        dataForm: {
          loginName: 'admin',
          password: '123456'
        },
        dataRule: {
          loginName: [
            {required: true, message: '帐号不能为空', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '密码不能为空', trigger: 'blur'}
          ]
        },
        dataList:[]
      }
    },
    created () {
      // this.getAuthorization()
    },
    methods: {
      // 提交表单
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl('/login'),
              method: 'post',
              data: new URLSearchParams(this.dataForm)
            }).then(({data}) => {
              console.log('data')
              console.log(data)
              if (data && data.code === 200) {
                this.$router.replace({name: 'home'})
              } else {
                // this.getAuthorization()
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 获取授权(本项目目前不需要获取authorization)
      getAuthorization () {
        this.$http({
          url: this.$http.adornUrl('authorization'),
          method: 'get'
        }).then(({data}) => {
          this.$cookie.set('token', data.data.authorization)
        })
      },

      modifyPassword () {
        var modifyPassword = {
          password:'123456',
          oldPassword:'123456'
        }
        this.$http({
          url: this.$http.adornUrl('/admin/modify/password'),
          method: 'post',
          data: new URLSearchParams(modifyPassword)
        }).then(({data}) => {
          console.log('修改密码返回的data')
          console.log(data)
          if (data && data.code === 200) {
            console.log('密码修改成功')
            this.$router.replace({name: 'home'})
          } else {
            this.$message.error(data.msg)
          }
        })
      },

      //  获取用户信息列表
      getUserList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/user/list'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNo': this.pageNo,
            'pageSize': this.pageSize,
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      addUser(){
        var userInfo = {
          username: 'wdeq',
          password: 'dwqr32',
          email: 'ew qbuqbh.@qq.com',
          mobile: '2453253252'
        }
        this.$http({
          url: this.$http.adornUrl('/user/add'),
          method:'post',
          data: new URLSearchParams(userInfo)
        })
      },
      // 编辑用户信息
      editUser(id){
        var editUserInfo = {
          username: 'wdeq',
          password: 'dwqr32',
          email: 'renmay.@gmail.com',
          mobile: '2453253252',
          id:'92482937849720'
        }
        this.$http({
          url: this.$http.adornUrl('/user/edit'),
          method:'post',
          data: new URLSearchParams(editUserInfo)
        })
      },


      // 修改用户的密码
      modifyUserPassword(){
        var modifyUserPassword = {
          username: 'wdeq',
          password: 'dwqr32',
          email: 'renmay.@gmail.com',
          mobile: '2453253252',
          id:'92482937849720'
        }
        this.$http({
          url: this.$http.adornUrl('/user/modify/password'),
          method:'post',
          data: new URLSearchParams(modifyUserPassword)
        })
      },

      // 重置用户信息
      resetUserInfo(id){
        var modifyUserPassword = {
          id:'92482937849720'
        }
        this.$http({
          url: this.$http.adornUrl('/user/reset/password'),
          method:'post',
          data: new URLSearchParams(modifyUserPassword)
        })
      },

      // 删除用户
      deleteUser(id){
        this.$http({
          url: this.$http.adornUrl('/user/delete'),
          method:'post',
          data: new URLSearchParams(id)
        })
      },

      // 禁用用户
      disableUser(id){
        this.$http({
          url: this.$http.adornUrl('/user/disable'),
          method:'post',
          data: new URLSearchParams(id)
        })
      },

      // 启用用户
      enableUser(id){
        this.$http({
          url: this.$http.adornUrl('/user/enable'),
          method:'post',
          data: new URLSearchParams(id)
        })
      }
    }
  }
</script>

<style lang="scss">
  .site-wrapper.site-page--login {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(38, 50, 56, .6);
    overflow: hidden;
    &:before {
      position: fixed;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      height: 100%;
      content: "";
      background-image: url(~@/assets/img/bg.jpg);
      background-size: cover;
    }
    .site-content__wrapper {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      padding: 0;
      margin: 0;
      overflow-x: hidden;
      overflow-y: auto;
      background-color: transparent;
    }
    .site-content {
      min-height: 100%;
      padding: 30px 500px 30px 30px;
    }
    .brand-info {
      margin: 220px 100px 0 90px;
      color: #fff;
    }
    .brand-info__text {
      margin: 0 0 22px 0;
      font-size: 48px;
      font-weight: 400;
      text-transform: uppercase;
    }
    .brand-info__intro {
      margin: 10px 0;
      font-size: 16px;
      line-height: 1.58;
      opacity: .6;
    }
    .login-main {
      position: absolute;
      top: 0;
      right: 0;
      padding: 150px 60px 180px;
      width: 470px;
      min-height: 100%;
      background-color: #fff;
    }
    .login-title {
      font-size: 16px;
    }
    .login-captcha {
      overflow: hidden;
      > img {
        width: 100%;
        cursor: pointer;
      }
    }
    .login-btn-submit {
      width: 100%;
      margin-top: 38px;
    }
  }
</style>
