<template>
  <div class="index">
    <div v-if="!isStart" class="welcome">
      <div class="welcome-title">名前を入力してください</div>
      <input type="text" v-model="name">
      <button class="start-btn" @click="start()">はじめる</button>
      <div v-if="isError" class="error">{{ errorMessage }}</div>
    </div>
    <div v-if="isStart" class="btn-wrapper">
      <div class="oya">
      <button class="btn btn--yellow btn--cubic" @click="excite()">盛り上がっている</button>
      </div>
      <div class="oya">
      <button class="btn btn--green btn--cubic" @click="normal()">普通</button>
      </div>
    </div>
    <div v-if="isStart" class="oya">
      <button class="result" @click="sendResult()">集計を終了する</button>
    </div>
    <div v-if="isSend" class="message">送信しています...</div>
  </div>
</template>

<script>
export default {
  async asyncData ({ app }) {
    const res = await app.$axios.get('https://hirota-lab.microcms.io/api/v1/data?limit=1000', {
      headers: {
        'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
      }
    })
    const dataList = res.data.contents

    // 同じ日に同じ名前で登録されるのを防ぐために、今日登録された人の名前をリスト化しておく
    const todayList = dataList.filter(x => x.day === app.$dayjs().format('MM月DD日'))
    const alreadyNameList = []
    todayList.forEach((elem) => {
      alreadyNameList.push(elem.name)
    })

    return { alreadyNameList }
  },
  data () {
    return {
      isSend: false,
      name: '',
      isStart: false,
      isError: false,
      exciteList: [],
      normalList: [],
      errorMessage: '名前を入力してください'
    }
  },
  methods: {
    excite () {
      this.exciteList.push(this.$dayjs().format('HH:mm:ss'))
    },
    normal () {
      this.normalList.push(this.$dayjs().format('HH:mm:ss'))
    },
    async sendResult () {
      this.isSend = true
      await this.$axios.$post('https://hirota-lab.microcms.io/api/v1/data', {
        exciteDataList: JSON.stringify(this.exciteList),
        normalDataList: JSON.stringify(this.normalList),
        day: this.$dayjs().format('MM月DD日'),
        name: this.name
      }, {
        headers: {
          'Content-Type': 'application/json',
          'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
        }
      }).then(() => {
          alert('集計が完了しました。')
          location.reload()
        }).catch(() => {
          alert('エラーが発生しました。')
        })
    },
    start () {
      if (this.name) {
        if (this.alreadyNameList.includes(this.name)) {
          this.errorMessage = 'こちらの名前は本日すでに終了しています。\n他の名前でお試しください。'
          this.isError = true      
        } else {
          this.isError = false
          this.isStart = true
        }
      } else {
        this.errorMessage = '名前を入力してください'
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
  margin-top: 100px;
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
  width: 50%;
  height: 100px;
  display: flex;
  justify-content: center;  
  margin-top: 1rem;
}

@media screen and (max-width: 760px) {
  .oya {
    width: 100%;
  }
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
  width: 80%;
  text-align: center;
  font-size: 0.8rem;
  color: #f54848;
  margin-top: 0.5rem;
}

.message-area {
  width: 100%;
  height: 50px;
  display: flex;
  justify-content: center;
}

.result {
  padding: 0 1rem;
  height: 40px;
  margin-top: 40px;
}
</style>