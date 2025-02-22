<template>
  <view class="write-comment-box">
    <input
      ref="input"
      :focus="focus"
      :placeholder="placeholderText"
      :value="newCommentReq.text"
      class="write-comment"
      type="text"
      @blur="blur"
      @focus="onFocus"
      @input="$emit('updateText', $event.target.value)"
    />
    <view class="like-box">
      <view
        v-if="likeData.isLike"
        class="like-icon liked"
        @click="$emit('doLike')"
      />
      <view v-else class="like-icon" @click="$emit('doLike')" />
      <view class="like-num">
        {{ likeData.count }}
      </view>
    </view>
    <view class="send-comment-btn" @click="localCreateComment"> 发布</view>
  </view>
</template>

<script lang="ts" setup>
import { NewCommentReq } from "@/apis/comment/comment-interfaces";
import { createComment, LikeStruct } from "@/pages/moment/utils";
import { reactive } from "vue";

// eslint-disable-next-line no-unused-vars
const props = defineProps<{
  newCommentReq: NewCommentReq;
  likeData: LikeStruct;
  placeholderText: string;
  focus: boolean;
}>();

const emit = defineEmits<{
  (e: "updateText", newText: string): void;
  (e: "update:placeholderText", newText: string): void;
  (e: "doLike"): void;
  (e: "afterCreateComment"): void;
  (e: "afterBlur"): void;
}>();

const preReq = reactive<NewCommentReq>({
  id: props.newCommentReq.id,
  scope: "moment",
  text: ""
});

const localCreateComment = async () => {
  let res = null;
  if (props.focus) {
    res = await createComment(props.newCommentReq);
  } else {
    res = await createComment({
      id: preReq.id,
      scope: preReq.scope,
      text: props.newCommentReq.text
    });
  }
  if (res) emit("afterCreateComment");
};

const onFocus = () => {
  preReq.id = props.newCommentReq.id;
  preReq.scope = props.newCommentReq.scope;
};

const blur = () => {
  emit("update:placeholderText", "发布评论");
  emit("afterBlur");
};
</script>

<style lang="scss" scoped>
@import "@/common/icon.scss";

.write-comment-box {
  position: fixed;
  bottom: 0;
  left: 0;
  z-index: 50;
  display: flex;
  width: 100vw;
  box-sizing: border-box;
  align-items: center;
  height: 16vw;
  box-shadow: 0 -1px 2px #eee;
  padding: 0 30rpx;
  background-color: #fafafa;
  .write-comment {
    width: 64%;
    height: 36px;
    background-color: #fafafa;
    border-radius: 35rpx;
    padding-left: 30rpx;
    margin-right: 12px;

    .uni-input-placeholder {
      color: #d1d1d1;
    }
  }

  .like-box {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    margin-right: 12px;

    .like-icon {
      width: calc(20 / 390 * 100vw);
      height: calc(20 / 390 * 100vw);
      background-size: 100% 100%;
      background-image: $like-grey-border-url;

      &.liked {
        background-image: $like-blue-url;
      }
    }

    .like-num {
      max-width: 40px;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      font-size: 12px;
      color: #c8c8c8;
    }
  }

  .send-comment-btn {
    background-color: #1fa1ff;
    border-radius: 39rpx;
    width: 132rpx;
    padding: 0 10rpx;
    line-height: 35px;
    font-size: 16px;
    text-align: center;
    color: #fff;
  }
}
</style>
