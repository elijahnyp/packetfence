<template>
  <b-card no-body>
    <pf-config-list
      :config="config"
    >
      <template slot="pageHeader">
        <b-card-header><h4 class="mb-0" v-t="'Devices'"></h4></b-card-header>
      </template>
      <template slot="buttonAdd">
        <b-button variant="outline-primary" :to="{ name: 'newDevice' }">{{ $t('Add Device') }}</b-button>
      </template>
      <template slot="emptySearch" slot-scope="state">
        <pf-empty-table :isLoading="state.isLoading">{{ $t('No devices found') }}</pf-empty-table>
      </template>
      <template slot="buttons" slot-scope="item">
        <span class="float-right text-nowrap">
          <b-button size="sm" variant="outline-primary" class="mr-1" @click.stop.prevent="clone(item)">{{ $t('Clone') }}</b-button>
          <pf-button-delete  v-if="!item.not_deletable" size="sm" variant="outline-danger" :disabled="isLoading" :confirm="$t('Delete Device?')" @on-delete="remove(item)" reverse/>
        </span>
      </template>
    </pf-config-list>
  </b-card>
</template>

<script>
import pfButtonDelete from '@/components/pfButtonDelete'
import pfConfigList from '@/components/pfConfigList'
import pfEmptyTable from '@/components/pfEmptyTable'
import {
  pfConfigurationProfilingDevicesListConfig as config
} from '@/globals/configuration/pfConfigurationProfiling'

export default {
  name: 'ProfilingDevicesList',
  components: {
    pfButtonDelete,
    pfConfigList,
    pfEmptyTable
  },
  data () {
    return {
      devices: [], // all devices
      config: config(this)
    }
  },
  methods: {
    clone (item) {
      this.$router.push({ name: 'cloneDevice', params: { id: item.id } })
    },
    remove (item) {
      this.$store.dispatch('$_profiling/deleteDevice', item.id).then(response => {
        this.$router.go() // reload
      })
    }
  },
  created () {
    this.$store.dispatch('$_profiling/devices').then(data => {
      this.devices = data
    })
  }
}
</script>
