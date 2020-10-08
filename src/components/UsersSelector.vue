<template>
  <b-form-tags v-model="internalValue" size="lg" add-on-change no-outer-focus class="mb-2">
    <template v-slot="{ tags, inputAttrs, inputHandlers, disabled, removeTag }">
      <b-row align-h="start" align-v="center">
        <b-form-select
            class="inline-select mx-2"
            v-bind="inputAttrs"
            v-on="inputHandlers"
            :disabled="disabled || availableOptions.length === 0"
            :options="availableOptions"
          >
          <template v-slot:first>
            <!-- This is required to prevent bugs with Safari -->
            <option disabled value="">Choose a tag...</option>
          </template>
        </b-form-select>
        <ul v-if="tags.length > 0" class="list-inline d-inline-block mb-0">
          <li v-for="tag in tags" :key="tag" class="list-inline-item mr-0">
            <b-form-tag
              @remove="removeTag(tag)"
              :title="options[tag]"
              :disabled="disabled"
              variant="info"
            />
          </li>
        </ul>
      </b-row>
    </template>
  </b-form-tags>
</template>

<script>
export default {
  props: {
    options: {
      required: true,
    },
    value: {
      required: true,
    }
  },
  data() {
    return {
      internalValue: this.value
    }
  },
  computed: {
    availableOptions() {
      let res = [];
      for (let key in this.options)
        if (this.internalValue.indexOf(key) === -1)
          res.push(key);
      return res;
    }
  },
  watch: {
    value(newValue) {
      this.internalValue = newValue;
    },

    internalValue(newValue) {
      this.$emit('input', newValue);
    }
  }

}
</script>

<style>
.inline-select {
  width: 2rem;
}
</style>