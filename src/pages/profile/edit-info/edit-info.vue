<template>
  <TopBar :has-go-back="true">
    <template #center>修改个人资料</template>
  </TopBar>
  <view class="container">
    <image :src="Pictures.ProfileBackground" class="bg-set" />
    <view class="choose-avatar-row">
      <button
        class="avatar-wrapper"
        open-type="chooseAvatar"
        @chooseavatar="onChooseAvatar"
      >
        <image
          :src="userInfo.avatarUrl ? userInfo.avatarUrl : props.avatarUrl"
          class="avatar"
        />
      </button>
      <text class="avatar-hint">点击更换头像</text>
    </view>
    <form @submit="formSubmit">
      <text class="nickname">昵称</text>
      <view class="choose-nickname-row">
        <input
          :value="userInfo.nickname ? userInfo.nickname : props.nickname"
          class="update-nickname"
          name="nickname"
          placeholder="请输入昵称"
          type="nickname"
        />
      </view>
      <view class="motto">个性签名</view>
      <view class="motto-input">
        <textarea
          :value="userInfo.motto ? userInfo.motto : props.motto"
          class="textarea-inherit"
          name="motto"
          placeholder="&nbsp 请输入新的个性签名"
          placeholder-style="color: #B8B8B8;"
          type="motto"
        />
      </view>
      <button
        :disabled="disableConfirm"
        class="confirm-change"
        form-type="submit"
      >
        <text class="save">确认</text>
      </button>
    </form>
  </view>
</template>

<script lang="ts" setup>
import { updateUserInfo } from "@/apis/user/user";
import { UpdateUserInfoReq } from "@/apis/user/user-interfaces";
import { Prefixes, putObject } from "@/apis/cos/cos";
import { reactive, ref } from "vue";
import { Pages, Pictures } from "@/utils/url";
import TopBar from "@/components/TopBar.vue";

const props = defineProps<{
  avatarUrl: string;
  nickname: string;
  motto: string;
}>();
const disableConfirm = ref(false);
const userInfo = reactive<UpdateUserInfoReq>({});

function onChooseAvatar(e: any) {
  disableConfirm.value = true;
  putObject({
    filePath: e.detail.avatarUrl,
    prefix: Prefixes.Avatar
  }).then((res) => {
    disableConfirm.value = false;
    userInfo.avatarUrl = res.url;
  });
}

function formSubmit(e: any) {
  userInfo.nickname = e.detail.value.nickname;
  userInfo.motto = e.detail.value.motto;
  updateUserInfo(userInfo)
    .then(() => {
      uni.showToast({
        title: "修改信息成功"
      });
      setTimeout(() => {
        uni.reLaunch({
          url: Pages.Profile
        });
      }, 1000);
    })
    .catch((res: UniNamespace.RequestSuccessCallbackResult) => {
      if (res.statusCode === 400) {
        uni.showToast({
          icon: "error",
          title: "该用户名已存在",
          duration: 1000
        });
      }
    });
}
</script>

<style lang="scss" scoped>
.content {
  width: 100%;
  height: 40%;
  background-color: #eeeeee;
}

.bg-set {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  z-index: -1;
  opacity: 0.9; //设置透明度
}

.choose-avatar-row {
  width: 100%;
  height: 80%;
  align-items: center;
  text-align: center;
  font-size: 28rpx;
  margin-bottom: 10%;

  .avatar-wrapper {
    width: 260rpx;
    height: 260rpx;
    margin-top: 40rpx;
    margin-bottom: 40rpx;
    border-radius: 50%;

    .avatar {
      width: 260rpx;
      height: 260rpx;
      border-radius: 50%;
      margin-left: -25rpx;
    }
  }
}

.avatar-hint {
  font-size: 30rpx;
  color: #888888;
}

.nickname {
  margin-left: 70rpx;
  font-size: 40rpx;
  color: #858585;
}

.choose-nickname-row {
  width: 92%;
  height: 60rpx;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  padding: 20rpx 4rpx;
  margin-top: 20rpx;
  margin-left: 30rpx;
  margin-bottom: 40rpx;
  background-color: #f8f8f8;
  border: 5rpx solid #f1f1f1;
  box-shadow: 0 0 5px 3px rgba(0, 0, 0, 0.02);
  border-radius: 25rpx;

  .update-nickname {
    margin-left: 40rpx;
    font-size: 40rpx;
  }
}

.confirm-change {
  margin-top: 30rpx;
  margin-left: 30rpx;
  height: 100rpx;
  width: 93%;
  border-radius: 25rpx;
  background-color: #1fa1ff;

  .save {
    color: #fcfcfc;
    font-size: 40rpx;
  }
}

.motto {
  margin-left: 70rpx;
  font-size: 40rpx;
  color: #858585;
}

.motto-input {
  margin-top: 30rpx;
  margin-left: 30rpx;
  font-size: 30rpx;
  width: 92%;
  font-size: 30rpx;
  display: flex;
  background-color: #f8f8f8;
}

.textarea-inherit {
  margin-left: 30rpx;
  font-size: 32rpx;
  width: 92%;
  overflow: auto;
  word-break: break-all; //解决兼容问题
}
</style>
