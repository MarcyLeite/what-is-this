<template>
  <v-form ref="form" class="d-flex justify-center pa-16" :disabled="loading">
    <v-sheet :width="812">
      <v-progress-linear v-if="loading" indeterminate />
      <div class="pa-8 d-flex flex-column ga-4 rounded">
        <div class="text-h5">Cadastrar Nota</div>
        <v-textarea v-model:model-value="comentario" label="Comentario" no-resize :rules="rules" />
        <div class="d-flex ga-4">
          <v-text-field v-model="date" disabled label="Data" :rules="rules" />
          <v-btn color="secondary" :disabled="loading"> Pick Date
            <v-overlay
              activator="parent"
              location-strategy="connected"
              scroll-strategy="block"
            >
              <v-date-picker v-model:model-value="date" color="secondary" :rules="rules" />
            </v-overlay>
          </v-btn>
        </div>
        <v-number-input
          v-model:model-value="gravidade"
          control-variant="split"
          label="Gravidade"
          :max="10"
          :min="0"
        />
        <v-select
          v-model:model-value="trabalhoId"
          :items="trabalhoList.map(t => ({
            title: `${t.titulo} - ${t.grupo}`,
            value: t.id
          }))"
          label="Trabalho"
          :rules="rules"
        />
        <!-- <v-select
          v-model:model-value="trabalhoId"
          :items="userList.map(u => ({
            title: u.nome,
            value: u.id
          }))"
          label="Usuario"
          :rules="rules"
        /> -->
        <div class="d-flex justify-end">
          <v-btn color="primary" :disabled="loading" @click="submit()">
            Salvar
          </v-btn>
        </div>
      </div>
    </v-sheet>
  </v-form>
</template>

<script setup lang="ts">
  import type { TaskRaw } from './index.vue'
  import axios from 'axios'
  import { onMounted, ref } from 'vue'
  import { useRouter } from 'vue-router'

  const form = ref<any>()

  const loading = ref(false)

  const comentario = ref('')
  const date = ref<Date>()
  const gravidade = ref<number>()
  const trabalhoId = ref<number>()

  const rules = [
    (value: string) => {
      if (value) return true
      return 'No empty field'
    },
  ]

  const router = useRouter()

  const trabalhoList = ref<TaskRaw[]>([])

  const fetchTrabalhoList = async () => {
    const response = await axios.get('http://localhost:8080/trabalho')
    trabalhoList.value = response.data
  }

  const submit = async () => {
    if (!form.value) return
    const { valid } = await form.value.validate()

    if (!valid) return

    try {
      loading.value = true
      await axios.post('http://localhost:8080/revisao', {
        comentario: comentario.value,
        dataHora: date.value,
        gravidade: gravidade.value,
        trabalho: {
          id: trabalhoId.value,
        },
      })

      router.push('/review')
    } catch {
      loading.value = false
    }
  }

  onMounted(async () => {
    loading.value = true
    await fetchTrabalhoList()
    loading.value = false
  })
</script>
