<template>
  <b-form-group horizontal :label-cols="(columnLabel) ? labelCols : 0" :label="$t(columnLabel)"
    :state="isValid()" :invalid-feedback="getInvalidFeedback()"
    class="pf-form-chosen" :class="{ 'mb-0': !columnLabel, 'is-focus': focus }">
    <b-input-group>
      <multiselect
        v-model="inputValue"
        v-bind="$attrs"
        v-on="forwardListeners"
        ref="input"
        :showLabels="false"
        :id="id"
        :multiple="multiple"
        :options="options"
        :trackBy="trackBy"
        :label="label"
        :groupValues="groupValues"
        :state="isValid()"
        @input.native="validate()"
        @keyup.native.stop.prevent="onChange($event)"
        @change.native="onChange($event)"
        @open="focus = true"
        @close="focus = false"
      >
        <b-media slot="noResult" class="text-secondary" md="auto">
          <icon name="search" scale="2" slot="aside" class="ml-2"></icon>
          <strong>{{ $t('No results') }}</strong>
          <b-form-text class="font-weight-light">{{ $t('Please refine your search.') }}</b-form-text>
        </b-media>
      </multiselect>
    </b-input-group>
    <b-form-text v-if="text" v-t="text"></b-form-text>
  </b-form-group>
</template>

<script>
import Multiselect from 'vue-multiselect'
import 'vue-multiselect/dist/vue-multiselect.min.css'
import pfMixinValidation from '@/components/pfMixinValidation'

export default {
  name: 'pf-form-chosen',
  mixins: [
    pfMixinValidation
  ],
  components: {
    Multiselect
  },
  props: {
    value: {
      default: null
    },
    columnLabel: {
      type: String
    },
    labelCols: {
      type: Number,
      default: 3
    },
    text: {
      type: String,
      default: null
    },
    options: {
      type: Array,
      default: () => { return [] }
    },
    multiple: {
      type: Boolean,
      default: false
    },
    id: {
      type: String
    },
    trackBy: {
      type: String,
      default: 'value'
    },
    label: {
      type: String,
      default: 'text'
    },
    groupValues: {
      type: String
    },
    // Add a proxy on our inputValue to modify set/get for simple external models.
    // https://github.com/shentao/vue-multiselect/issues/385#issuecomment-418881148
    collapseObject: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      focus: false
    }
  },
  computed: {
    inputValue: {
      get () {
        const currentValue = this.value || ((this.multiple) ? [] : null)
        if (this.collapseObject) {
          const options = (!this.groupValues)
            ? this.options
            : this.options.reduce((options, group, index) => { // flatten group
              options.push(...group[this.groupValues])
              return options
            }, [])
          return (this.multiple)
            ? [...new Set(currentValue.map(value => options.find(option => option[this.trackBy] === value)))]
            : (!options)
              ? null
              : options.find(option => option[this.trackBy] === currentValue)
        }
        return currentValue
      },
      set (newValue) {
        if (this.collapseObject) {
          newValue = (this.multiple)
            ? [...new Set(newValue.map(value => value[this.trackBy]))]
            : (newValue && newValue[this.trackBy])
        }
        this.$emit('input', newValue)
      }
    },
    forwardListeners () {
      const { input, ...listeners } = this.$listeners
      return listeners
    }
  }
}
</script>

<style lang="scss">
@import "../../node_modules/bootstrap/scss/functions";
@import "../../node_modules/bootstrap/scss/mixins/border-radius";
@import "../../node_modules/bootstrap/scss/mixins/box-shadow";
@import "../../node_modules/bootstrap/scss/mixins/transition";
@import "../styles/variables";

/**
 * Adjust is-invalid and is-focus borders
 */
.pf-form-chosen {
  .multiselect {
      min-height: auto;
      border-width: 1px;
      font-size: $font-size-base;
  }
  .multiselect__tags {
    min-height: auto;
    padding: $input-padding-y $input-padding-x;
    border: 1px solid $input-focus-bg;
    background-color: $input-focus-bg;
    @include border-radius($border-radius);
    @include transition($custom-forms-transition);
    outline: 0;
    .multiselect__input {
      max-width: 100%
    }
    span > span.multiselect__single { /* placeholder */
      color: $input-placeholder-color;
      // Override Firefox's unusual default opacity; see https://github.com/twbs/bootstrap/pull/11526.
      opacity: 1;
    }
    .multiselect__tag {
      margin-bottom: 0px;
    }
  }
  .multiselect__input,
  .multiselect__single {
    padding: 0px;
    margin: 0px;
    background-color: $input-focus-bg;
    color: $input-color;
    font-size: $font-size-base;
    line-height: $input-line-height;
    &::placeholder {
      color: $input-placeholder-color;
    }
  }
  .multiselect__placeholder {
    padding-top: 0px;
    margin-bottom: 0px;
    color: $input-placeholder-color;
    font-size: $font-size-base;
    line-height: $input-line-height;
  }
  .multiselect__content-wrapper {
    border: $dropdown-border-width solid $dropdown-border-color;
    @include border-radius($dropdown-border-radius);
    @include box-shadow($dropdown-box-shadow);
  }
  .multiselect--active:not(.multiselect--above) {
    .multiselect__content-wrapper {
      border-top-width: 0px;
      border-bottom-width: 1px;
      border-top-left-radius: 0 !important;
      border-top-right-radius: 0 !important;
      border-bottom-left-radius: $border-radius !important;
      border-bottom-right-radius: $border-radius !important;
    }
  }
  .multiselect--above {
    .multiselect__content-wrapper {
      border-bottom-width: 0px;
      border-bottom-left-radius: 0 !important;
      border-bottom-right-radius: 0 !important;
    }
  }
  .multiselect__option--highlight {
    background-color: $dropdown-link-active-bg;
    color: $dropdown-link-active-color;
  }
  &.is-focus {
    .multiselect__tags {
      border-color: $input-focus-border-color;
    }
  }
  &.is-invalid {
    .multiselect__tags {
      border-color: $form-feedback-invalid-color;
    }
  }
}
</style>
