<template>
  <div class="login">
    <div class="bglayer"></div>
    <div class="home_header">登录</div>
    <div class="content">
      <span class="head_img">
        <img src="@/assets/icon/home/userImg.png" alt="">
      </span>
      <p class="user_name">
        <img src="@/assets/icon/login/手机IC.png" alt="">
        <input v-model="userInfo.name" placeholder="请输入登陆账号" type="text">
      </p>
      <p class="password">
        <img class="pass" src="@/assets/icon/login/密码IC.png" alt="">
        <input @blur.native.capture="checkInputName" :state="NameStatus" v-model="userInfo.password" :type="type" placeholder="请输入登陆密码">
        <img @click='changeType' class="eye" :src="img" alt="">
      </p>
      <p class="info">
        <span @click='goRePassword'>忘记密码？</span>
      </p>
      <div @click='handleLogin' class="login_btn">登录</div>
      <div @click='goRegister' class="register_btn">注册</div>
    </div>
  </div>
</template>

<script>
import { Switch, Toast, DatetimePicker, Indicator } from 'mint-ui'
import { mapMutations, mapGetters } from 'vuex'
export default {
  name: 'login',
  data () {
    return {
      type: 'password',
      img: require('@/assets/icon/login/eye—open.png'),
      userInfo: {
        name: '',
        password: ''
      },
      NameStatus:'',
    }
  },
  computed: {
    // ...mapMutations(['setUser'])
  },
  methods: {
    ...mapMutations(['setUser']),
    checkInputName(){ 
      // 判断是全为汉字
      var regex = /^[\u4E00-\u9FA5]+$/
      var name = this.userInfo.name
      if (!regex.test(name)) {
        this.NameStatus="error"
      } else {
        this.NameStatus="success"
      }
    },
    changeType () {
      if (this.type === 'text') {
        this.type = 'password'
        this.img = require('@/assets/icon/login/eye—open.png')
      } else {
        this.type = 'text'
        this.img = require('@/assets/icon/login/eye—close.png')
      }
    },
    handleLogin () {
      const data = {
        appuserNumber: this.userInfo.name,
        appuserPassword: this.userInfo.password
      }
      if (!this.userInfo.name || !this.userInfo.password) {
        return Toast({
          message: '登陆信息不能为空',
          iconClass: 'icon icon-success'
        })
      }
      Indicator.open('登陆中...')

      let timer = setTimeout(() => {
        Indicator.close()
        Toast({
          message: '服务器出错',
          iconClass: 'icon icon-error'
        })
      }, 5000)
      this.$http.post(`${config.httpBaseUrl}/appuser/login`, data).then(res => {
        // clearTimeout(timer)
        // Indicator.close();
        // if (res.code === 200) {
          this.$http.get(`${config.httpBaseUrl}/appuser/getappuser`, {
            params: {
              number: this.userInfo.name
            }}).then(res => {
            // if (res.code === 200) {
              this.setUser(res.date.appuser)
              localStorage.setItem('user', JSON.stringify(res.date.appuser))
              this.$router.push({
                name: 'Home'
              })
            // }
          })
        // } else {
        //   Toast({
        //     message: '登陆失败\n账号和密码输入错误',
        //     iconClass: 'icon icon-success'
        //   })
        // }
      })
    },
    goRePassword () {
      this.$router.push({
        name: 'ResetPassword'
      })
    },
    goRegister () {
      this.$router.push({
        name: 'Register'
      })
    }
  }
}
</script>

<style lang='scss' scoped>
.login {
  width: 100vw;
  height: 100vh;
  background: #49BA94;
  .bglayer {
    width: 100vw;
    height: .48rem;
    background: #49BA94;
  }
  .home_header {
    font-size: 0.36rem;
    text-align: center;
    color: white;
    height: 0.96rem;
    line-height: 0.96rem;
    padding: 0 0.36rem;
  }
  .content {
    text-align: center;
    width: 5.5rem;
    margin: 0 auto;
    font-size: .26rem;
    .head_img {
      background: white;
      display: inline-block;
      margin-top: 40px;
      width: 1.4rem;
      height: 1.4rem;
      padding: 5px;
      border-radius: 50%;
      img {
        border-radius: 50%;
        width: 100%;
        height: 100%;
      }
    }
    .user_name {
      background:rgba(255,255,255,0.6);
      display: flex;
      border-radius: .45rem;
      height: .9rem;
      padding: 0 .45rem;
      align-items: center;
      margin: 20px 0;
      img {
        width: .3rem;
        height: .32rem;
      }
      input {
        width: 100%;
        background: transparent;
        height: .32rem;
        margin-left: 10px;
      }
    }
    .password {
      background:rgba(255,255,255,0.6);
      display: flex;
      border-radius: .45rem;
      height: .9rem;
      padding: 0 .45rem;
      align-items: center;
      input {
        width: 100%;
        background: transparent;
        height: .32rem;
        margin-left: 10px;
      }
      img.pass {
        width: .3rem;
        height: .32rem;
      }
      img.eye {
        width: .36rem;
        height: .16rem;
      }
    }
    .info {
      color: #FFFFFF;
      font-size: .24rem;
      margin: 10px 0;
    }
    .login_btn {
      width: 5.50rem;
      height: .90rem;
      line-height: .90rem;
      color: #15BF86;
      font-size: .38rem;
      background:rgba(255,255,255,1);
      box-shadow: 0px 2px 6px 0px rgba(20,146,104,0.8);
      border-radius: .45rem;
      margin: 50px 0 20px;
    }
    .register_btn {
      color: white;
      position: relative;
      &:after {
        position: absolute;
        right: 0;
        top: 50%;
        content: '';
        display: inline-block;
        width: 2.1rem;
        height: 1px;
        background:rgba(255,255,255,1);
      }
      &:before {
        position: absolute;
        left: 0;
        top: 50%;
        content: '';
        display: inline-block;
        width: 2.1rem;
        height: 1px;
        background:rgba(255,255,255,1);
      }
    }
  }
}
</style>
