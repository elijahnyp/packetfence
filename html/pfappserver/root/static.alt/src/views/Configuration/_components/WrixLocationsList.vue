<template>
  <b-card no-body>
    <pf-config-list
      :config="config"
    >
      <template slot="pageHeader">
        <b-card-header>
          <h4 class="mb-3" v-t="'WRIX'"></h4>
        </b-card-header>
      </template>
      <template slot="buttonAdd">
        <b-button variant="outline-primary" :to="{ name: 'newWrixLocation' }">{{ $t('Add WRIX Location') }}</b-button>
      </template>
      <template slot="emptySearch" slot-scope="state">
        <pf-empty-table :isLoading="state.isLoading">{{ $t('No WRIX locations found') }}</pf-empty-table>
      </template>
      <template slot="buttons" slot-scope="item">
        <span class="float-right text-nowrap">
          <b-button size="sm" variant="outline-primary" class="mr-1" @click.stop.prevent="clone(item)">{{ $t('Clone') }}</b-button>
          <pf-button-delete  v-if="!item.not_deletable" size="sm" variant="outline-danger" :disabled="isLoading" :confirm="$t('Delete WRIX Location?')" @on-delete="remove(item)" reverse/>
        </span>
      </template>
      <template slot="status" slot-scope="data">
        <icon name="circle" :class="{ 'text-success': data.status === 'enabled', 'text-danger': data.status === 'disabled' }"
          v-b-tooltip.hover.left.d300 :title="$t(data.status)"></icon>
      </template>
    </pf-config-list>
  </b-card>
</template>

<script>
import pfButtonDelete from '@/components/pfButtonDelete'
import pfConfigList from '@/components/pfConfigList'
import pfEmptyTable from '@/components/pfEmptyTable'
import {
  pfConfigurationWrixLocationsListConfig as config
} from '@/globals/configuration/pfConfigurationWrixLocations'

export default {
  name: 'WrixLocationsList',
  components: {
    pfButtonDelete,
    pfConfigList,
    pfEmptyTable
  },
  data () {
    return {
      config: config(this)
    }
  },
  methods: {
    clone (item) {
      this.$router.push({ name: 'cloneWrixLocation', params: { id: item.id } })
    },
    remove (item) {
      this.$store.dispatch('$_wrix_locations/deleteWrixLocation', item.id).then(response => {
        this.$router.go() // reload
      })
    }
  }
}
</script>
