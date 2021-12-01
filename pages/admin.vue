<template>
  <div class="admin">
    <select v-model="selectedDay">
      <option disabled value="">日付を選択してください</option>
      <option v-for="(elem, i) in dayList" :key="i" :value="elem">
        {{ elem }}
      </option>
    </select>
    <select v-if="selectedDay" v-model="selectedName">
      <option disabled value="">名前を選択してください</option>
      <option v-for="(elem, i) in filterdBySelectedDayData" :key="i" :value="elem.name">
        {{ elem.name }}
      </option>
    </select>
    <div class="result-wrapper">
      <div class="result-container">
        <div class="title">盛り上がっている</div>

        <button class="download" @click="downloadExciteList()">csvダウンロード</button>
        <div v-for="elem in exciteData" :key="elem.id" class="result">
          {{ elem }}
        </div>
      </div>
      <div class="result-container">
        <div class="title">普通</div>
        <button class="download" @click="downloadNormalList()">csvダウンロード</button>
        <div v-for="elem in normalData" :key="elem.id" class="result">
          {{ elem }}
        </div>
      </div>
    </div>
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

    let dayList = []

    dataList.forEach((elem) => {
      if (!dayList.includes(elem.day)) {
        dayList.push(elem.day)
      }
    })

    return { dataList, dayList }
  },
  data () {
    return {
      dayList: [],
      selectedDay: '',
      selectedName: '',
      nameListOfSelectedData: [],
      filterdBySelectedDayData: [],
      allFilterdData: [],
      exciteData: [],
      normalData: []
    }
  },
  methods: {
    downloadExciteList () {
      let output = '\ufeff' + this.selectedName + ',' + this.selectedDay
      this.exciteData.forEach((elem) => {
        output += ',' + elem
      })
      let blob = new Blob([output], { type: 'text/csv' })
      let link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = this.selectedName + '_' + this.selectedDay + '_盛り上がっているデータ.csv'
      link.click()
    },
    downloadNormalList () {
      let output = '\ufeff' + this.selectedName + ',' + this.selectedDay
      this.normalData.forEach((elem) => {
        output += ',' + elem
      })
      let blob = new Blob([output], { type: 'text/csv' })
      let link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = this.selectedName + '_' + this.selectedDay + '_普通データ.csv'
      link.click()
    },
    format () {
      console.log(JSON.parse(this.allFilterdData[0].exciteDataList))
      this.exciteData = JSON.parse(this.allFilterdData[0].exciteDataList).slice()
      this.exciteData.reverse()
      this.normalData = JSON.parse(this.allFilterdData[0].normalDataList).slice()
      this.normalData.reverse()
    },
    init () {
      this.allFilterdData = []
      this.exciteData = []
      this.normalData = []
    }
  },
  watch: {
    selectedDay () {
      this.filterdBySelectedDayData = this.dataList.filter(x => x.day === this.selectedDay)
      this.init()
    },
    selectedName () {
      this.allFilterdData = this.filterdBySelectedDayData.filter(x => x.name === this.selectedName)
      this.format()
    }
  }
}
</script>


<style scoped>
.admin {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  margin-top: 20px;
}

.title {
  display: inline-block;
  border-bottom: 2px solid #e7e7e7;
}

.result-wrapper {
  width: 100%;
  display: flex;
  justify-content: space-around;
}

select {
  max-width: 600px;
  width: 80%;
  height: 40px;
  margin: 1rem 0;
  padding: 0 1rem;
}

.result-container {
  display: flex;
  flex-direction: column;
}

.download {
  margin: 1rem 0;
}

</style>