<template>
  <div class="table-container">
    <el-button @click="resetInfinite">reset</el-button>
    <el-table :data="list" style="with: 100%">
      <el-table-column prop="id" label="id" />
      <el-table-column prop="name" label="name" />
      <el-table-column prop="email" label="email" width="180" />
      <iniinfinite-loading
        slot="append"
        :identifier="infiniteId"
        force-use-in-infinite-wrapper=".el-table__body-wrapper"
        @infinite="infiniteHandler"
      />
    </el-table>
  </div>
</template>

<script>
// import InfiniteLoading from 'vue-infinite-loading'
export default {
  data() {
    return {
      accounts: [],
      list: [],
      limit: 15,
      page: 0,
      infiniteId: +new Date()
    }
  },
  created() {
    for (let i = 0; i < 300; i++) {
      var account = {
        id: i + 1,
        name: `test_${i + 1}`,
        email: `test_${i + 1}@example.com`
      }
      this.accounts.push(account)
    }
    this.fetchList(this.limit)
  },
  methods: {
    infiniteHandler($state) {
      if (this.accounts.length && this.accounts.length > this.list.length) {
        let scrollLimit = (this.page + 1) * this.limit
        if (this.accounts.length < scrollLimit) {
          scrollLimit = this.accounts.length
        }
        this.fetchList(scrollLimit)
        $state.loaded()
      } else {
        $state.complete()
      }
    },
    fetchList(scrollLimit) {
      this.list.push(
        ...this.accounts.slice(this.page * this.limit, scrollLimit)
      )
      this.page++
    },
    resetInfinite() {
      this.page = 0
      this.list = []
      this.fetchList(this.limit)
      this.infiniteId++
      const elm = document.getElementsByClassName('el-table__body-wrapper')[0]
      elm.scrollTo({ top: 0 })
    }
  }
}
</script>

<style>
.table-container > .el-table > .el-table__body-wrapper {
  overflow: scroll;
  max-height: calc(100vh - 100px);
}
</style>
