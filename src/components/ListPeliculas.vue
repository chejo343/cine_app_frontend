<template>
  <v-card>
    <v-card-title class="white--text primary">
      Películas
    </v-card-title>
    <v-card-text>
      <v-row>
        <v-col
          v-for="item in items"
          :key="item.id"
          cols="12"
          md="4"
          xl="3"
        >
          <v-card>
            <v-img :src="item.poster"></v-img>
            <div class="d-flex align-center fill-height" style="height: 81px;">
              <v-card-title class="text-center subtitle-1">{{ item.nombre }}</v-card-title>
            </div>
            <v-rating
              :value="item.data.vote_average / 2"
              half-increments
              dense
              readonly
              size="14"
              color="yellow darken-3"
              background-color="transparent"
              class="mx-4"
            />
            <v-card-actions>
              <v-btn
                text
                color="secondary"
                :to="{ name: 'Pelicula', params: { pelicula: item } }"
              >Sinopsis</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    items: {
      type: Array,
      default: () => []
    }
  }
}
</script>
