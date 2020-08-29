<template>
  <v-card>
    <v-card-text>
      <v-row>
        <v-col cols="12" md="6">
          <v-img :src="pelicula.poster"></v-img>
        </v-col>
        <v-col cols="12" md="6">
          <h1 class="h4 primary--text py-1 mt-5">{{ pelicula.nombre }}</h1>
          <h2 class="h6 primary--text mt-1 mt-2">{{ genero }}</h2>
          <v-rating
            :value="pelicula.data.vote_average / 2"
            half-increments
            dense
            readonly
            size="14"
            color="yellow darken-3"
            background-color="transparent"
            class="my-2"
          />
          <v-chip color="secondary" class="mb-5">Duración {{ pelicula.data.runtime }} min.</v-chip>
          <p class="my-1 subtitle-1 font-weight-bold primary--text">Sinopsis</p>
          <p class="my-1 body-1">{{ pelicula.data.overview }}</p>
          <p class="my-1 subtitle-1 font-weight-bold primary--text">Funciones</p>
          <SelectFecha @input="getFunciones" />
          <FuncionesPelicula :items="funciones" />
          <div v-if="pelicula.youtube_id">
            <p class="mt-5 subtitle-1 font-weight-bold primary--text">Trailer</p>
            <div class="ma-lg-5 video-responsive">
              <iframe
                width="560"
                height="315"
                :src="`https://www.youtube.com/embed/${pelicula.youtube_id}`"
                frameborder="0"
                allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
                allowfullscreen
              ></iframe>
            </div>
          </div>
          <p class="my-1 subtitle-1 font-weight-bold primary--text">Actores</p>
          <v-row>
            <v-col
              v-for="item in cast"
              :key="item.id"
              cols="6"
              md="4"
              lg="3"
            >
              <v-card>
                <v-img :src="item.photo"></v-img>
                <v-card-text>
                  <p class="subtitle-2 text-center my-1">{{ item.name }}</p>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
import dayjs from 'dayjs'
import FuncionesPelicula from '@/components/FuncionesPelicula'
import SelectFecha from '@/components/SelectFecha'
export default {
  components: {
    FuncionesPelicula,
    SelectFecha
  },
  props: {
    pelicula: {
      type: Object,
      default: () => null
    }
  },
  data () {
    return {
      funciones: []
    }
  },
  beforeMount () {
    if (!this.pelicula) {
      this.$router.go(-1)
    }
    const now = dayjs()
    this.getFunciones({
      fecha: now.format('YYYY-MM-DD'),
      hora: now.format('HH:mm:00.00')
    })
  },
  computed: {
    genero () {
      if (this.pelicula) {
        const text = this.pelicula.data.genres.length > 1 ? 'Generos: ' : 'Genero: '
        return text + this.pelicula.data.genres.map(i => i.name).join(', ')
      }
      return ''
    },
    cast () {
      if (this.pelicula) {
        const urlImg = process.env.VUE_APP_IMG
        const cast = this.pelicula.cast.length > 8 ? this.pelicula.cast.slice(0, 8) : this.pelicula.cast
        return cast.filter(i => i.profile_path).map(i => ({
          id: i.id,
          name: i.name,
          photo: `${urlImg}/w500${i.profile_path}`
        }))
      }
      return []
    }
  },
  methods: {
    async getFunciones (funcion) {
      const fecha = dayjs().format('YYYY-MM-DD')
      const { data } = await this.$api.get(`/funcions?pelicula=${this.pelicula.id}&fecha_eq=${funcion.fecha}&hora_inicio_gte=${funcion.hora}&_sort=sala:DESC`)
      this.funciones = data.map(i => ({
        ...i,
        hora_inicio: dayjs(`${fecha}T${i.hora_inicio}`).format('HH:mm')
      }))
    }
  }
}
</script>

<style>
.video-responsive {
  height: 0;
  overflow: hidden;
  padding-bottom: 56.25%;
  padding-top: 30px;
  position: relative;
}
.video-responsive iframe, .video-responsive object, .video-responsive embed {
  height: 100%;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
}
</style>
