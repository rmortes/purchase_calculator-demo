<template>
  <b-input-group>
    <template v-slot:prepend>
      <b-input-group-text
          @click="showPicker = !showPicker"
        >
        {{ user.emoji }}
      </b-input-group-text>
      <VEmojiPicker v-if="showPicker" @select="updateEmoji"/>
    </template>
    <b-form-input :placeholder="placeholder" v-model="user.username"></b-form-input>
  </b-input-group>
</template>

<script>
import VEmojiPicker from 'v-emoji-picker';

export default {
  components: {
    VEmojiPicker
  },
  props: {
    user: {
      required: true,
    },
    placeholder: {
      required: false,
    }
  },
  data() {
    return {
      showPicker: false,
    };
  },
  methods: {
    updateEmoji(emoji) {
      this.showPicker = false;
      this.user.emoji = emoji.data;
    }
  }
}
</script>

<style>
  .input-group-prepend {
    cursor: pointer;
  }

  .emoji-picker {
    position: absolute;
    z-index: 1;
  }
</style>