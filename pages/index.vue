<template>
  <div class="index">
    <div class="btn-wrapper">
      <button @click="excite()">盛り上がっている</button>
      <button @click="normal()">普通</button>
    </div>
    <div v-if="isSend" class="message">送信しました</div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isSend: false
    }
  },
  methods: {
    async excite () {
      await this.$axios.$post('https://hirota-lab.microcms.io/api/v1/excite', {
        date: this.$dayjs().format('MM月DD日 HH:mm:ss')
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
        date: this.$dayjs().format('MM月DD日 HH:mm:ss')
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
    }
  }
}
</script>


<style>
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
}

button {
  cursor: pointer;
}

.message {
  margin-top: 60px;
  font-size: 0.8rem;
  color: #4d9efc;
}
</style>