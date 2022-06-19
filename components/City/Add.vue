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
        <!-- lat -->
        <v-text-field
          outlined
          name="lat"
          label="lat"
          required
          v-model="lat"
        ></v-text-field>

        <!-- lng -->
        <v-text-field
          outlined
          name="lng"
          label="lng"
          required
          v-model="lng"
        ></v-text-field>
        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="Thumbnails"
          multiple
          v-model="files"
        ></v-file-input>
        <!-- display multiple image -->
        <div v-if="files">
          <img
            v-for="(image, index) in files"
            :key="index"
            :src="createImageUrl(image)"
            width="200"
          />
        </div>

        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="City image"
          v-model="file"
        ></v-file-input>
        <!-- display thumbnail -->
        <div v-if="file">
          <img width="200" :src="createImageUrl(file)" />
        </div>

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
      file: null,
      files: null,
      thumbnail: [],
      image: '',
      isLoading: false,
      status: '',
      message: '',
      lat: '',
      lng: '',
    }
  },

  watch: {
    dialog(status) {
      if (status == false) {
        document.getElementById('addCity').reset()
        this.status = ''
        this.message = ''
        this.title = ''
        this.description = ''
        this.file = ''
        this.files = ''
        this.lat = ''
        this.lng = ''
        this.image = ''
        this.thumbnail = []
      }
    },
  },

  methods: {
    //concat string reduce resolution image to 30-60kb
    addStr(str, index, stringToAdd) {
      return (
        str.substring(0, index) + stringToAdd + str.substring(index, str.length)
      )
    },
    async save() {
      this.isLoading = true

      let image_url = ''
      let thumbnail_url = []

      try {
        if (this.files) {
          for (let i = 0; i < this.files.length; i++) {
            let formData = new FormData()
            formData.append('file', this.files[i])
            const res = await this.$axios.post('/upload-image', formData)
            thumbnail_url.push(
              this.addStr(res.data.data, 50, 'w_700/h_400/q_60/')
            )
          }
        }
        this.thumbnail = thumbnail_url
        // console.log(this.thumbnail)

        if (this.file) {
          let formData = new FormData()
          formData.append('file', this.file)
          const res = await this.$axios.post('/upload-image', formData)
          image_url = res.data.data
        }
        this.image = image_url
        // console.log(this.image)

        await this.$axios.post('/city/create', {
          name: this.name,
          description: this.description,
          thumbnail: this.thumbnail,
          image: this.image,
          lat: this.lat,
          lng: this.lng,
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
    createImageUrl(file) {
      return URL.createObjectURL(file)
    },

    // handler(file) {
    //   this.file = file
    //   let image = document.getElementById('output')

    //   if (!file) {
    //     return (image.src = '')
    //   }

    //   image.src = URL.createObjectURL(file)
    // },
  },
}
</script>
