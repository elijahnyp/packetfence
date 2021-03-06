<template>
  <pf-config-view
    :isLoading="isLoading"
    :form="getForm"
    :model="form"
    :vuelidate="$v.form"
    @validations="formValidations = $event"
    @save="save"
  >
    <template slot="header" is="b-card-header">
      <h4 class="mb-0">
        <span>{{ $t('Cisco Mobility Services Engine') }}</span>
      </h4>
    </template>
    <template slot="footer">
      <b-card-footer @mouseenter="$v.form.$touch()">
        <pf-button-save :disabled="invalidForm" :isLoading="isLoading">
          <template>{{ $t('Save') }}</template>
        </pf-button-save>
      </b-card-footer>
    </template>
  </pf-config-view>
</template>

<script>
import pfConfigView from '@/components/pfConfigView'
import pfButtonSave from '@/components/pfButtonSave'
import {
  pfConfigurationCiscoMobilityServicesEngineViewFields as fields,
  pfConfigurationCiscoMobilityServicesEngineViewDefaults as defaults,
  pfConfigurationCiscoMobilityServicesEngineViewPlaceholders as placeholders
} from '@/globals/configuration/pfConfigurationCiscoMobilityServicesEngine'

const { validationMixin } = require('vuelidate')

export default {
  name: 'CiscoMobilityServicesEngineView',
  mixins: [
    validationMixin
  ],
  components: {
    pfConfigView,
    pfButtonSave
  },
  props: {
    storeName: { // from router
      type: String,
      default: null,
      required: true
    }
  },
  data () {
    return {
      form: defaults(this), // will be overloaded with the data from the store
      formValidations: {}, // will be overloaded with data from the pfConfigView
      placeholders: placeholders(this) // form placeholders
    }
  },
  validations () {
    return {
      form: this.formValidations
    }
  },
  computed: {
    isLoading () {
      return this.$store.getters['$_bases/isLoading']
    },
    invalidForm () {
      return this.$v.form.$invalid || this.$store.getters['$_bases/isWaiting']
    },
    getForm () {
      return {
        labelCols: 3,
        fields: fields(this)
      }
    }
  },
  methods: {
    save () {
      let form = JSON.parse(JSON.stringify(this.form)) // dereference
      this.$store.dispatch('$_bases/updateMseTab', form).then(response => {
        // TODO - notification
      })
    }
  },
  created () {
    this.$store.dispatch('$_bases/getMseTab').then(data => {
      this.form = Object.assign({}, data)
    })
  }
}
</script>
