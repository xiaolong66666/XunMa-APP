<template>
  <view class="normal-login-container">
    <view class="logo-content align-center justify-center flex">
      <image style="width: 100rpx;height: 100rpx;" :src="globalConfig.appInfo.logo" mode="widthFix">
      </image>
      <text class="title">寻码移动端登录</text>
    </view>
    <view class="login-form-content">
	  <view class="action-btn">
	    <button @click="handleLogin('guest')" class="login-btn cu-btn block bg-blue lg round">客户登录</button>
	  </view>
	  <view class="action-btn">
	    <button @click="handleLogin('custom')" class="login-btn cu-btn block bg-blue lg round">技术人员登录</button>
	  </view>
      <view class="action-btn">
        <button @click="handleLogin('admin')" class="login-btn cu-btn block bg-blue lg round">管理员登录</button>
      </view>
    </view>
     
  </view>
</template>

<script>
  export default {
    data() {
      return {
        globalConfig: getApp().globalData.config
      }
    },
    created() {
    },
    methods: {
      // 用户注册
      handleUserRegister() {
        this.$tab.redirectTo(`/pages/register`)
      },
      // 登录方法
      handleLogin(role) {
		if (role === "guest"){
			this.$tab.redirectTo('/pages/loginAdmin?user=guest')
		}else if(role === "custom"){
			this.$tab.redirectTo('/pages/loginAdmin?user=custom')
		}else{
			this.$tab.redirectTo('/pages/loginAdmin?user=admdin')
		}
      }
    }
  }
</script>

<style lang="scss">
  page {
    background-color: #ffffff;
  }

  .normal-login-container {
    width: 100%;

    .logo-content {
      width: 100%;
      font-size: 21px;
      text-align: center;
      padding-top: 15%;

      image {
        border-radius: 4px;
      }

      .title {
        margin-left: 10px;
      }
    }

    .login-form-content {
      text-align: center;
      margin: 20px auto;
      margin-top: 15%;
      width: 80%;

      .input-item {
        margin: 20px auto;
        background-color: #f5f6f7;
        height: 45px;
        border-radius: 20px;

        .icon {
          font-size: 38rpx;
          margin-left: 10px;
          color: #999;
        }

        .input {
          width: 100%;
          font-size: 14px;
          line-height: 20px;
          text-align: left;
          padding-left: 15px;
        }

      }

      .login-btn {
        margin-top: 40px;
        height: 45px;
      }
      
      .reg {
        margin-top: 15px;
      }
      
      .xieyi {
        color: #333;
        margin-top: 20px;
      }
      
      .login-code {
        height: 38px;
        float: right;
      
        .login-code-img {
          height: 38px;
          position: absolute;
          margin-left: 10px;
          width: 200rpx;
        }
      }
    }
  }

</style>
