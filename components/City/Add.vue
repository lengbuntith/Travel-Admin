<template>
  <v-dialog v-model="dialog" max-width="500">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="primary lighten-2" dark v-bind="attrs" v-on="on">
        New City
      </v-btn>
    </template>

    <v-card>
      <form id="addCity" @submit.prevent="save" class="pa-4">
        <v-text-field
          outlined
          name="name"
          label="City name"
          required
          v-model="name"
        ></v-text-field>
        <v-textarea
          outlined
          name="description"
          label="City description"
          required
          v-model="description"
        ></v-textarea>

        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="City thumbnail"
          @change="handler"
        ></v-file-input>

        <div>
          <img width="100%" id="output" />
        </div>

        <v-divider></v-divider>
        <v-card-actions class="d-flex">
          <v-btn
            :loading="isLoading"
            :disabled="isLoading"
            type="submit"
            color="primary"
            width="200"
            height="50"
            class="mr-3"
          >
            save
          </v-btn>

          <div class="red--text font-weight-bold" v-if="status == 'ERROR'">
            {{ message }}
          </div>

          <div class="green--text font-weight-bold" v-else>
            {{ message }}
          </div>
        </v-card-actions>
      </form>
    </v-card>
  </v-dialog>
</template>
<script>
export default {
  data() {
    return {
      dialog: false,
      name: '',
      description: '',
      file: '',
      isLoading: false,
      status: '',
      message: '',
    }
  },

  watch: {
    dialog(status) {
      if (status == false) {
        document.getElementById('addCity').reset()
        this.status = ''
        this.message = ''
        this.file = ''
        this.name = ''
        this.description = ''
      }
    },
  },

  methods: {
    async save() {
      this.isLoading = true

      let image_url = ''

      try {
        if (this.file) {
          let formData = new FormData()
          formData.append('file', this.file)
          const res = await this.$axios.post('/upload-image', formData)
          image_url = res.data.data
        }

        await this.$axios.post('/city/create', {
          name: this.name,
          description: this.description,
          thumbnail: image_url,
        })

        this.$nuxt.$emit('getCity')
        this.status = 'OK'
        this.message = 'Success'
        this.dialog = false
      } catch (error) {
        this.status = 'ERROR'
        this.message = 'Something went wrong'
      }

      setTimeout(() => {
        this.isLoading = false
      }, 2000)
    },

    handler(file) {
      this.file = file
      let image = document.getElementById('output')

      if (!file) {
        return (image.src = '')
      }

      image.src = URL.createObjectURL(file)
    },
  },
}
</script>
