<template>
  <div class="danmaku-item" :class="{ hidden: isHidden }">
    <div>
      <img v-if="showFace && face" class="danmaku-author-face" :src="face" />
    </div>

    <div class="content-wrapper">
      <div v-if="type === 'message'" class="danmaku-content">
        <div class="danmaku-author-wrapper">
          <span class="danmaku-author-name" :class="{ anchor: isAnchor, owner: isOwner }">{{ uname }}</span>
          <span v-if="title" class="badge">{{ title }}</span>
        </div>
        <div class="danmaku-message">{{ message }}</div>
      </div>
      <div v-else-if="type === 'gift'" class="danmaku-content">
        <span class="danmaku-author-name">{{ uname }}</span>
        <div class="danmaku-message">
          赠送 <span class="danmaku-gift-name">{{ giftName }}</span>
          <template v-if="num">&times;{{ num }}</template>
        </div>
      </div>
      <div v-else-if="type === 'sc'" class="danmaku-content">
        <span class="danmaku-author-name">{{ uname }}</span>
        <div class="danmaku-message">SC：{{ message }}</div>
      </div>
      <div v-else-if="type === 'info'" class="danmaku-content">
        <div class="danmaku-message info">{{ message }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import { toRefs, ref, computed, onBeforeUnmount } from 'vue';

export default {
  props: {
    type: {
      type: String,
      default: 'message',
    },
    showFace: Boolean,
    face: String,
    uid: Number,
    uname: String,
    message: String,
    isAnchor: Boolean,
    isOwner: Boolean,
    giftName: String,
    num: Number,
    stay: Number,
    hidden: Boolean,
    title: String,
  },
  setup(props) {
    const hidden = ref(false);
    const needRemoved = ref(false);
    if (props.stay) {
      const stayTimeout = setTimeout(() => {
        hidden.value = true;
        if (props.type === 'info') {
          setTimeout(() => (needRemoved.value = true), 800);
        }
      }, props.stay);
      onBeforeUnmount(() => clearTimeout(stayTimeout));
    }
    const isHidden = computed(() => props.hidden || hidden.value);
    return { ...toRefs(props), isHidden, needRemoved };
  },
};
</script>

<style lang="scss">
// use Font JinNan

.danmaku {
  &-item {
    padding: 6px 6px;
    display: flex;
    align-items: flex-start;
    background: rgba(104, 104, 104, 0.9);
    border-radius: 8px;
    margin: 4px 0;
    backdrop-filter: blur(9px);
    --webkit-backdrop-filter: blur(9px);
    max-width: 225px;
    transition: all 0.3s;
  }

  &-author-face {
    width: 32px;
    height: 32px;
    border-radius: 8px;
    margin-right: 12px;
    flex-shrink: 0;
  }

  .content-wrapper {
    flex: 1;
    min-width: 0;
  }

  &-author-wrapper {
    display: flex;
    align-items: center;
    gap: 0.4rem;
  }

  &-author-name {
    display: block;
    font-family: 'HeiTi';
    font-size: 13px;
    line-height: 1.2;
    margin-bottom: 2px;
    color: white;
    text-shadow: 1px 1px 4px black;

    &.anchor {
      color: #fff248;
      text-shadow: 1px 1px 4px rgb(206, 195, 0);
    }
    &.owner {
      color: #ff9800;
      text-shadow: 1px 1px 4px rgb(141, 85, 0);
    }
  }

  &-message {
    font-size: 17px;
    line-height: 1.3;
    color: #fff;
    word-break: break-word;
    font-family: 'HeiTi';
    &.info {
      color: #ccc;
      font-size: 14px;
    }
  }

  &-gift-name {
    color: #eb76ff;
    margin: 0 4px;
  }
}
.badge {
  background-color: rgb(46, 167, 238);
  color: #fff;
  padding: 2px 4px;
  border-radius: 8px;
  font-size: 12px;
  margin-left: 4px;
}
</style>
