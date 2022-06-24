<template>
  <v-dialog v-model="dialog" max-width="500">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="primary lighten-2" dark v-bind="attrs" v-on="on">
        New Place
      </v-btn>
    </template>

    <v-card>
      <form id="addPlace" @submit.prevent="save" class="pa-4">
        <!-- title -->
        <v-text-field
          outlined
          name="title"
          label="Title"
          required
          v-model="place.title"
        ></v-text-field>

        <!-- date -->
        <v-text-field
          outlined
          name="date"
          label="date"
          required
          v-model="place.date"
        ></v-text-field>

        <!-- story -->
        <v-textarea
          outlined
          name="story"
          label="story"
          required
          v-model="place.story"
        ></v-textarea>

        <!-- lat -->
        <v-text-field
          outlined
          name="lat"
          label="lat"
          required
          v-model="place.lat"
        ></v-text-field>

        <!-- lng -->
        <v-text-field
          outlined
          name="lng"
          label="lng"
          required
          v-model="place.lng"
        ></v-text-field>

        <!-- city -->
        <v-select
          :items="cities"
          v-model="place.city"
          item-text="name"
          return-object
          label="city"
        >
        </v-select>

        <!-- thumbnail -->
        <v-file-input
          prepend-icon="mdi-camera"
          accept="image/*"
          label="Thumbnail"
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
          label="Images"
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
      place: {
        title: '',
        thumbnail: '',
        images: '',
        date: '',
        story: '',
        lat: '',
        lng: '',
        city: '',
      },
      file: null,
      files: null,
      cities: [],
      isLoading: false,
      status: '',
      message: '',
    }
  },

  watch: {
    dialog(status) {
      if (status == false) {
        document.getElementById('addPlace').reset()
        this.status = ''
        this.message = ''
        this.file = null
        this.files = null
        this.place = {
          title: '',
          thumbnail: '',
          images: '',
          date: '',
          story: '',
          lat: '',
          lng: '',
          city: '',
        }
      } else {
        this.getCities()
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

      let thumbnail_url = ''
      let images_url = []

      try {
        if (this.file) {
          let formData = new FormData()
          formData.append('file', this.file)
          const res = await this.$axios.post('/upload-image', formData)
          thumbnail_url = this.addStr(res.data.data, 50, 'w_700/h_400/q_60/')
        }

        if (this.files) {
          for (let i = 0; i < this.files.length; i++) {
            let formData = new FormData()
            formData.append('file', this.files[i])
            const res = await this.$axios.post('/upload-image', formData)
            images_url.push(res.data.data)
          }
        }

        this.place.thumbnail = thumbnail_url
        this.place.images = images_url
        let { _id } = this.place.city
        this.place.city = _id

        await this.$axios.post('/place/create', this.place)

        this.$nuxt.$emit('getPlace')
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

    getCities() {
      this.$axios.get('/city/all').then((res) => (this.cities = res.data.data))
    },
  },
}
</script>
