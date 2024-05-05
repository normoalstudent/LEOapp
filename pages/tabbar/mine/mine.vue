<template>
  <view>
    <!-- 在屏幕中上部显示客户头像 -->
    <view class="customer-avatar-container">
      <image class="customer-avatar" src="@/static/LEOlogo.png"></image>
      <!-- 在events数组为空时也显示加号按钮 -->
      <view class="add-button" @click="addEvent(-1)">
        <image class="add-icon" src="@/static/add.png"></image>
      </view>
      <!-- 如果events数组为空，则显示提示信息 -->
      <view v-if="events.length === 0" class="empty-message">
        暂无事件内容
      </view>
    </view>
    <!-- 自定义事件模块 -->
    <view class="custom-events">
      <!-- 如果events数组不为空，则显示事件内容 -->
      <view v-if="events.length > 0">
        <view class="event" v-for="(event, index) in events" :key="index">
          <!-- 显示事件内容 -->
          <input class="event-content" v-model="events[index]" />
          <!-- 删除内容按钮 -->
          <view class="remove-button" @click="removeEvent(index)">
            <image class="remove-icon" src="@/static/remove.png"></image>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      events: [] // 存储自定义事件
    }
  },
  methods: {
    // 添加自定义事件
    addEvent(index) {
      // 在数组末尾添加一个空白事件
      this.events.push("");
    },
    // 删除自定义事件
    removeEvent(index) {
      // 移除指定索引的事件
      this.events.splice(index, 1);
    }
  }
}
</script>

<style>
.customer-avatar-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.customer-avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}

.custom-events {
  width: 80%;
  max-width: 400px; /* 设置最大宽度，防止内容太宽 */
  margin: 20px auto; /* 居中 */
}

.event {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.event-content {
  flex: 1;
}

.add-button,
.remove-button {
  width: 40px;
  height: 40px;
  background-color: #007AFF; /* 蓝色 */
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

.add-icon,
.remove-icon {
  width: 30px;
  height: 30px;
}

.remove-button {
  margin-left: 10px;
}

.empty-message {
  text-align: center;
  padding: 20px;
  color: #999;
}
</style>












