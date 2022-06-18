<template>
  <v-dialog v-model="dialog" max-width="500">
    <template v-slot:activator="{ on, attrs }">
      <v-icon small class="mr-2" v-bind="attrs" v-on="on"> mdi-pencil </v-icon>
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
        <v-text-field
          outlined
          name="lat"
          label="City lat"
          required
          v-model="lat"
        ></v-text-field>
        <v-textarea
          outlined
          name="lng"
          label="City lng"
          required
          v-model="lng"
        ></v-textarea>

        <!-- image -->
        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="image"
          v-model="file"
        ></v-file-input>

        <!-- display thumbnail -->
        <div v-if="file">
          <img width="200" :src="createImageUrl(file)" />
        </div>

        <!-- images -->
        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="thumbnails"
          v-model="files"
          multiple
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

        <!-- <div>
          <img width="100%" id="output" />
        </div> -->

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
  props: ['id'],
  data() {
    return {
      dialog: false,
      name: '',
      description: '',
      file: null,
      files: null,
      lat: '',
      lng: '',
      thumbnail: [],
      image: '',
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
        this.files = ''
        this.title = ''
        this.image = ''
        this.thumbnail = []
        this.lat = ''
        this.lng = ''
      } else {
        this.$axios.get(`/city/detail/${this.id}`).then((res) => {
          this.name = res.data.data.name
          this.description = res.data.data.description
          this.thumbnail = res.data.data.thumbnail
          this.image = res.data.data.image
          this.lat = res.data.data.lat
          this.lng = res.data.data.lng
        })
      }
    },
  },

  methods: {
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
              this.addStr(res.data.data, 50, 'w_800/h_600/q_50/')
            )
          }
        }
        this.thumbnail = thumbnail_url
        if (this.file) {
          let formData = new FormData()
          formData.append('file', this.file)
          const res = await this.$axios.post('/upload-image', formData)
          image_url = res.data.data
        }
        this.image = image_url

        await this.$axios.post(`/city/update/${this.id}`, {
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
