<template>
  <view class="page">
    <!-- 摄像头图标 -->
    <view class="camera-icon" @click="openCamera">
      <image src="@/static/camera.png" class="camera-image"></image>
    </view>

    <!-- 原有聊天界面 -->
    <view class="chat-container">
      <!-- 聊天消息 -->
      <view class="chat-messages">
        <!-- 显示聊天消息 -->
        <view class="chat-message" v-for="(message, index) in messages" :key="index">
          <!-- 根据消息发送者显示不同的样式 -->
          <view class="message-container" :class="{ 'user': message.sender === 'user', 'bot': message.sender === 'bot' }">
            <!-- 显示消息文本 -->
            <view class="message-text">{{ message.text }}</view>
          </view>
        </view>
      </view>
      <!-- 语音识别结果 -->
      <view class="speech-text" v-if="speechText">{{ speechText }}</view>
      <!-- 文本输入框和语音录制图标 -->
      <view class="input-container">
        <input class="text-input" v-model="inputText" placeholder="" @keyup.enter="sendMessage" />
        <image v-if="recording" class="record-icon" src="@/static/speak.png" @click="stopRecording"></image>
        <image v-else class="record-icon" src="@/static/speakn.png" @click="startRecording"></image>
      </view>
    </view>
  </view>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      messages: [], // 存储聊天消息
      recognition: null, // 语音识别对象
      recording: false, // 记录录音状态
      speechText: "", // 存储语音识别的文本
      inputText: "" // 存储文本输入框的内容
    };
  },
  mounted() {
    // 初始化语音识别对象
    try {
      this.recognition = new webkitSpeechRecognition();
      this.recognition.lang = 'zh-CN'; // 设置识别语言为中文
      this.recognition.continuous = true;
      this.recognition.interimResults = true;
      this.recognition.onresult = this.handleSpeechRecognitionResult; // 处理识别结果的回调函数
    } catch (error) {
      console.error('语音识别对象初始化失败：', error);
      // 在这里进行错误处理，例如显示错误提示或者重新初始化
    }
  },
  methods: {
    // 开始录音
    startRecording() {
      if (this.recognition) {
        this.recording = true;
        this.recognition.start();
      } else {
        console.error('语音识别对象未正确初始化');
      }
    },
    // 结束录音
    stopRecording() {
      if (this.recognition) {
        this.recording = false;
        this.recognition.stop();
      } else {
        console.error('语音识别对象未正确初始化');
      }
    },
    // 处理语音识别结果
    handleSpeechRecognitionResult(event) {
      // 获取识别到的文本
      let transcript = "";
      for (let i = event.resultIndex; i < event.results.length; i++) {
        if (event.results[i].isFinal) {
          transcript += event.results[i][0].transcript;
        }
      }
      // 更新语音识别的文本
      this.speechText = transcript;
    },
    // 发送消息
    sendMessage() {
      const text = this.inputText.trim();
      if (text === "") return;
      // 添加用户发送的消息到消息列表中
      this.messages.push({ sender: "user", text: text });

      // 清空文本输入框
      this.inputText = "";

      // 调用API发送文本并获取响应
      this.sendTextToAPI(text);
    },
    // 发送文本到API并获取响应
    sendTextToAPI(text) {
      const apiKey = "lm-IgQGu/B4TrIahdG4EpUKzA==";
      const serviceName = "aca-chat-send";
      const url = "https://nlp.aliyuncs.com/v2/api/chat/send";
      const headers = {
        "Content-Type": "application/json",
        "X-AcA-DataInspection": "enable",
        "x-fag-servicename": serviceName,
        "x-fag-appcode": "aca",
        "Authorization": `Bearer ${apiKey}`
      };
      const payload = {
        "input": {
          "messages": [
            {"role":"user","name":"小明","content": text}
          ],
          "aca": {
            "botProfile": {
              "characterId": "720bbaf861ee449a8e9466b5cb36522e"
            },
            "userProfile": {
              "userId": "1602713100050584",
              "userName": "小明",
              "basicInfo": ""
            },
            "scenario": {
              "description": "我是小明，是你的朋友。"
            },
            "context": {
              "useChatHistory": false
            }
          }
        }
      };

      axios.post(url, payload, { headers })
        .then((response) => {
          console.log(response.status);
          console.log(response.data);
          // 将机器人的响应添加到消息列表中
          const botResponse = response.data; // 从响应中提取机器人的回复
          this.messages.push({ sender: "bot", text: botResponse });
        })
        .catch((error) => {
          console.error("API请求错误:", error);
          // 在这里进行错误处理，例如显示错误提示
        });
    },
    // 打开摄像头
    openCamera() {
      // 触发选择摄像头的操作
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'image/*';
      input.capture = 'environment';
      input.onchange = (event) => {
        const file = event.target.files[0];
        if (file) {
          // 在这里处理获取到的文件，可以上传到服务器或者进行其他操作
          // 这里只是简单地打印文件信息
          console.log('选择的文件：', file);
        }
      };
      input.click();
    }
  }
};
</script>

<style scoped>
.camera-icon {
  position: absolute;
  top: 90%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.camera-image {
  width: 30px;
  height: 30px;
  cursor: pointer;
}
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.chat-messages {
  flex: 1;
  overflow-y: auto;
}
.message-container {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 10px; /* 修改这里以增加消息之间的间距 */
}
.message-text {
  max-width: 70%;
  padding: 10px;
  border-radius: 8px;
  background-color: #DCF8C6; /* 蓝色 */
}
.message-container.user .message-text {
  background-color: #E6F2FF; /* 绿色 */
}
.input-container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 10px;
  background-color: #f2f2f2;
}
.record-icon {
  width: 50px;
  height: 50px;
  cursor: pointer;
}
.text-input {
  flex: 1;
  margin-left: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
.speech-text {
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}
</style>




