<template>
  <div class="index">
    <div v-if="!isStart" class="welcome">
      <div class="welcome-title">名前を入力してください</div>
      <input type="text" v-model="name">
      <button class="start-btn" @click="start()">はじめる</button>
      <div v-if="isError" class="error">名前を入力してください</div>
    </div>
    <div v-if="isStart" class="btn-wrapper">
      <div class="oya">
      <button class="btn btn--yellow btn--cubic" @click="excite()">盛り上がっている</button>
      </div>
      <div class="oya">
      <button class="btn btn--green btn--cubic" @click="normal()">普通</button>
      </div>
    </div>
    <div class="message-area">
      <div v-if="isSend" class="message">送信しました</div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isSend: false,
      name: '',
      isStart: false,
      isError: false
    }
  },
  methods: {
    async excite () {
      await this.$axios.$post('https://hirota-lab.microcms.io/api/v1/excite', {
        date: this.$dayjs().format('MM月DD日 HH:mm:ss'),
        name: this.name
      }, {
        headers: {
          'Content-Type': 'application/json',
          'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
        }
      }).then(() => {
        this.send()
        }).catch(() => {
          alert('エラーが発生しました')
        })
    },
    async normal () {
            await this.$axios.$post('https://hirota-lab.microcms.io/api/v1/normal', {
        date: this.$dayjs().format('MM月DD日 HH:mm:ss'),
        name: this.name
      }, {
        headers: {
          'Content-Type': 'application/json',
          'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
        }
      }).then(() => {
        this.send()
        }).catch(() => {
          alert('エラーが発生しました')
        })
    },
    send () {
      this.isSend = true
      setTimeout(() => {
        this.isSend = false
      }, 1500)
    },
    start () {
      if (this.name) {
        this.isError = false
        this.isStart = true
      } else {
        this.isError = true
      }
      
    }
  }
}
</script>


<style scoped>
.index {
  width: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.btn-wrapper {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-around;
  margin-top: 50px;
}

@media screen and (max-width: 760px) {
  .btn-wrapper {
    flex-direction: column;
  }
}

button {
  cursor: pointer;
}

.oya {
  width: 100%;
  height: 100px;
  display: flex;
  justify-content: center;  
  margin-top: 1rem;
}

.message {
  margin-top: 60px;
  font-size: 0.8rem;
  color: #4d9efc;
}

.btn,
button.btn,
button.btn {
  max-width: 300px;
  width: 90%;
  height: 70px;
  font-size: 1rem;
  font-weight: 700;
  line-height: 1.5;
  position: relative;
  display: inline-block;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-transition: all 0.3s;
  transition: all 0.3s;
  text-align: center;
  vertical-align: middle;
  text-decoration: none;
  letter-spacing: 0.1em;
  color: #212529;
  border-radius: 0.5rem;
  border: none;
}

button.btn--yellow {
  color: #000;
  background-color: #fff100;
  border-bottom: 5px solid #ccc100;
}

button.btn--yellow:hover {
  margin-top: 3px;
  color: #000;
  background: #fff20a;
  border-bottom: 2px solid #ccc100;
}

button.btn--yellow:active {
  margin-top: 3px;
  color: #000;
  background: #fff20a;
  border-bottom: 2px solid #ccc100;
}

button.btn--green {
  color: #ffffff;
  background-color: #2a9c90;
  border-bottom: 5px solid #2c5369;
}

button.btn--green:hover {
  margin-top: 3px;
  color: #ffffff;
  background: #2a9c90;
  border-bottom: 2px solid #2c5369;
}

.welcome {
  max-width: 800px;
  width: 90%;
  max-height: 500px;
  height: 80vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  border-radius: 45px;
  filter: drop-shadow(0 3px 6px #00000016);
  background-color: #ffffff;
}

.welcome-title {
  margin-top: 100px;
}

input {
  width: 80%;
  height: 40px;
  margin-top: 100px;
  border: 2px solid #cccccc;
  border-radius: 5px;
  padding: 0 0.5rem;
}

.start-btn {
  height: 40px;
  padding: 0 2rem;
  margin-top: 100px;
}

.error {
  font-size: 0.8rem;
  color: #f54848;
}

.message-area {
  width: 100%;
  height: 50px;
  display: flex;
  justify-content: center;
}
</style>