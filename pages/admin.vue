<template>
  <div class="admin">
    <div class="result-wrapper">
      <div class="title">盛り上がっている</div>
      <div v-for="elem in exciteList" :key="elem.id" class="result">
        {{ elem.date }}
      </div>
    </div>
    <div class="result-wrapper">
      <div class="title">普通</div>
      <div v-for="elem in normalList" :key="elem.id" class="result">
        {{ elem.date }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData ({ app }) {
    const exciteData = await app.$axios.get('https://hirota-lab.microcms.io/api/v1/excite', {
      headers: {
        'X-MICROCMS-API-KEY': '842b109162ba4f8680ca4fbde4e2a7c3079c'
      }
    })
    const normalData = await app.$axios.get('https://hirota-lab.microcms.io/api/v1/normal', {
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
    return { exciteList: exciteList, normalList: normalList }
  }
}
</script>


<style>
.admin {
  width: 100%;
  display: flex;
  justify-content: space-around;
  margin-top: 60px;
}

.title {
  display: inline-block;
  border-bottom: 2px solid #e7e7e7;
}
</style>