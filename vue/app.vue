<template>
  <div>
      <div style="display: flex; align-items: center; justify-content: center">
        <a-form-item :validate-status="errors != '' ? 'error' : '' " :help="errors" >
          <a-input v-model="ipSubnet" @pressEnter="getData()" id="errors" placeholder="Enter an IP subnet to search" />
        </a-form-item>
      </div>
      <div style="display: flex; align-items: center; justify-content: center">
          <a-button type="primary" @click="getData()" :loading="loading">Search</a-button>
      </div>
    <a-table :columns="columns" :data-source="dataSource" :loading="loading"></a-table>
  </div>

</template>

<script>
export default {
  data () {
    return {
      errors: '',
      loading: false,
      dataSource: [],
      ipSubnet: '',
      columns: [
        { dataIndex: 'ipNumber', title: 'IP', width: 100 },
        { dataIndex: 'macAddr', title: 'MAC Address', width: 100 },
        { dataIndex: 'vendor', title: 'Vendor', width: 100 }
      ]
    }
  },
  methods: {
    async getData () {
      this.errors = ''
      this.loading = true
      await this.$rpc.call('networkScanning', 'get', { ipSubnet: this.ipSubnet })
        .then(res => {
          this.dataSource = JSON.parse(res[0]).map((v, i) => {
            v.key = i
            return v
          })
          this.errors = ''
        })
        .catch(err => {
          this.dataSource = []
          const temp = JSON.parse(err)
          const regex = new RegExp(/(?<= )[^\]]+/)
          this.errors = regex.exec(temp.error.data)[0]
        })
      this.loading = false
    }
  }
}
</script>
