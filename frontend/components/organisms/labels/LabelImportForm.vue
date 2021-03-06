<template>
  <base-card
    :disabled="!valid"
    @agree="create"
    @cancel="cancel"
    title="Upload Label"
    agree-text="Upload"
    cancel-text="Cancel"
  >
    <template #content>
      <v-form
        ref="form"
        v-model="valid"
      >
        <v-alert
          v-show="showError"
          v-model="showError"
          type="error"
          dismissible
        >
          The file could not be uploaded. Maybe invalid format.
          Please check available formats carefully.
        </v-alert>
        <h2>Example format</h2>
        <code class="mb-10 pa-5 highlight">
          <span>{{ exampleFormat }}</span>
        </code>
        <h2>Select a file</h2>
        <v-file-input
          v-model="file"
          :rules="uploadFileRules"
          accept=".json"
          label="File input"
        />
      </v-form>
    </template>
  </base-card>
</template>

<script>
import BaseCard from '@/components/molecules/BaseCard'
import { uploadFileRules } from '@/rules/index'

export default {
  components: {
    BaseCard
  },
  props: {
    uploadLabel: {
      type: Function,
      default: () => {},
      required: true
    }
  },
  data() {
    return {
      valid: false,
      file: null,
      uploadFileRules,
      showError: false
    }
  },

  computed: {
    exampleFormat() {
      const data = [
        {
          text: 'Dog',
          suffix_key: 'a',
          background_color: '#FF0000',
          text_color: '#ffffff'
        },
        {
          text: 'Cat',
          suffix_key: 'c',
          background_color: '#FF0000',
          text_color: '#ffffff'
        }
      ]
      console.log(JSON.stringify(data, null, 4))
      return JSON.stringify(data, null, 4)
    }
  },

  methods: {
    cancel() {
      this.$emit('close')
    },
    validate() {
      return this.$refs.form.validate()
    },
    reset() {
      this.$refs.form.reset()
    },
    create() {
      if (this.validate()) {
        this.uploadLabel({
          projectId: this.$route.params.id,
          file: this.file
        })
          .then((response) => {
            this.reset()
            this.cancel()
          })
          .catch(() => {
            this.showError = true
          })
      }
    }
  }
}
</script>

<style scoped>
  .highlight {
    font-size: 100%;
    width: 100%;
  }
  .highlight:before {
    content: ''
  }
</style>
