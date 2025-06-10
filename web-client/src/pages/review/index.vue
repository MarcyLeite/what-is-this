<template>
  <div class="d-flex justify-center pa-16">
    <v-sheet class="pa-8 d-flex flex-column ga-4 rounded" :width="812">
      <div class="d-flex flex-column align-end">
        <div class="d-flex ga-4 w-100">
          <v-number-input
            v-model:model-value="searchGravidade"
            control-variant="split"
            label="Gravidade"
            :max="10"
            :min="0"
          />
          <v-text-field v-model:model-value="searchTitulo" label="Titulo" />
        </div>
      </div>
      <div class="d-flex justify-space-between align-center">
        <div class="text-h5">Review</div>
        <v-btn color="secondary" @click="fetchNoteList()"> Search </v-btn>
      </div>
      <v-divider />
      <v-data-table :items="reviewList" />
      <div class="d-flex justify-end">
        <v-btn color="primary" to="/review/register">Nova</v-btn>
      </div>
    </v-sheet>
  </div>
</template>

<script setup lang="ts">
  import axios from 'axios'
  import { onMounted, ref } from 'vue'

  export type TaskRaw = {
    id: number
    titulo: string
    grupo: string
    nota: number
  }

  type ReviewRaw = {
    id: number
    comentario: string
    trabalho: TaskRaw
    gravidade: number
  }

  type Review = {
    id: number
    comentario: string
    titulo: string
    grupo: string
    estado: string
  }

  const searchGravidade = ref<number | null>(null)
  const searchTitulo = ref<string | null>(null)

  const reviewList = ref<Review[]>([])

  const mapResponse = (response: any): Review[] => {
    return response.data.map((review: ReviewRaw) => {
      const task = review.trabalho
      let estado: string
      if (task.nota !== null && review.gravidade > 3) {
        estado = 'fatal'
      } else if (task.nota === null && review.gravidade > 3) {
        estado = 'grave'
      } else {
        estado = 'pendente'
      }
      return {
        id: review.id,
        comentario: review.comentario,
        titulo: task.titulo,
        grupo: task.grupo,
        estado,
      }
    })
  }

  const fetchNoteList = async () => {
    const sufix = searchGravidade.value !== null && searchTitulo.value !== null ? `/${searchTitulo.value}/${searchGravidade.value}`.replaceAll(' ', '%20') : ''
    console.log(sufix)
    console.log(`http://localhost:8080/revisao${sufix}`)
    const response = await axios.get(`http://localhost:8080/revisao${sufix}`)
    console.log(response.data)
    reviewList.value = mapResponse(response)
    // const response = await axios.get(`http://localhost:8080/anotacao${sufix}`)
    // noteList.value = mapResponse(response)
  }

  onMounted(() => {
    fetchNoteList()
  })
</script>
