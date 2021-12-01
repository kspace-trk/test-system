<template>
  <div class="admin">
    <select v-model="selectedName">
      <option disabled value="">選択してください</option>
      <option v-for="(elem, i) in nameList" :key="i" :value="elem">
        {{ elem }}
      </option>
    </select>
    <div class="result-wrapper">
      <div class="result-container">
        <div class="title">盛り上がっている</div>

        <button class="download" @click="downloadExciteList()">csvダウンロード</button>
        <div v-for="elem in filterdExciteList" :key="elem.id" class="result">
          {{ elem.date }}
        </div>
      </div>
      <div class="result-container">
        <div class="title">普通</div>
        <button class="download" @click="downloadNormalList()">csvダウンロード</button>
        <div v-for="elem in filterdNormalList" :key="elem.id" class="result">
          {{ elem.date }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData ({ app }) {
    const exciteData = await app.$axios.get('https://hirota-lab.microcms.io/api/v1/excite?limit=1000', {
      headers: {
        'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
      }
    })
    const normalData = await app.$axios.get('https://hirota-lab.microcms.io/api/v1/normal?limit=1000', {
      headers: {
        'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
      }
    })

    let exciteList
    let normalList

    await Promise.all([
      exciteData,
      normalData
    ]).then((values) => {
      exciteList = values[0].data.contents;
      normalList = values[1].data.contents;
    });

    let nameList = []

    exciteList.forEach((elem) => {
      // nameListに名前がなかった場合にnameListに名前を追加
      if (!nameList.includes(elem.name)) {
        nameList.push(elem.name)
      }
    })

    console.log(nameList)

    return {
      exciteList: exciteList,
      normalList: normalList,
      nameList: nameList
    }
  },
  data () {
    return {
      selectedName: '',
      filterdExciteList: [],
      filterdNormalList: [],
    }
  },
  methods: {
    downloadExciteList () {
      let output = '\ufeff'
      this.filterdExciteList.forEach((elem) => {
        output += elem.date + ','
      })
      let blob = new Blob([output], { type: 'text/csv' })
      let link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = this.selectedName + '_盛り上がっているデータ.csv'
      link.click()
    },
    downloadNormalList () {
      let output = '\ufeff'
      this.filterdNormalList.forEach((elem) => {
        output += elem.date + ','
      })
      let blob = new Blob([output], { type: 'text/csv' })
      let link = document.createElement('a')
      link.href = window.URL.createObjectURL(blob)
      link.download = this.selectedName + '_普通データ.csv'
      link.click()
    }
  },
  watch: {
    selectedName () {
      console.log(this.selectedName)
      this.filterdExciteList = this.exciteList.filter(x => x.name === this.selectedName)
      this.filterdNormalList = this.normalList.filter(x => x.name === this.selectedName)
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