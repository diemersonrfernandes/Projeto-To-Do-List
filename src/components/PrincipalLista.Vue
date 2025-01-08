<template>
  <div
    class="q-pa-md fundo"
    style="height: 100vh; display: flex; justify-content: center; align-items: flex-start"
  >
    <!-- Contêiner da imagem -->
    <div class="image-container">
      <img src="../assets/tick.png" alt="Logo" class="logo" />
    </div>

    <!-- Card principal -->
    <q-card class="q-pa-md" style="width: 100%; max-width: 800px; margin-top: 180px">
      <!-- Título -->
      <div class="text-center q-mb-md">
        <h1 class="text-primary text-h5">MINHA LISTA DE TAREFAS</h1>
      </div>

      <!-- Campos para adicionar uma nova tarefa -->
      <div class="q-mb-md">
        <q-input
          v-model="novaTarefa.titulo"
          filled
          dense
          placeholder="Título da tarefa"
          class="q-mb-sm"
        />
        <q-input
          v-model="novaTarefa.descricao"
          filled
          dense
          placeholder="Descrição da tarefa"
          class="q-mb-sm"
        />
      </div>

      <!-- Filtros e botões -->
      <div class="q-mb-md row items-center justify-between">
        <!-- Botão para adicionar uma nova tarefa -->
        <q-btn
          label="Adicionar Tarefa"
          color="primary"
          @click="adicionarTarefa"
          class="col-auto q-mr-sm"
        />

        <!-- Filtros por status e ordem, visíveis somente se houver tarefas -->
        <div class="row items-center" v-if="tarefas.length > 0">
          <q-select
            v-model="filtroStatus"
            :options="opcoesStatus"
            label="Filtrar por Status"
            dense
            filled
            style="min-width: 180px"
            class="q-mr-sm"
          />
          <q-select
            v-model="ordemOrdenacao"
            :options="opcoesOrdenacao"
            label="Ordenar por Data"
            dense
            filled
            style="min-width: 180px"
          />
        </div>
      </div>

      <!-- Exibição das tarefas -->
      <q-list bordered>
        <q-item v-for="(tarefa, indice) in tarefas" :key="indice" class="q-mb-md">
          <q-item-section>
            <div>
              <h5>{{ tarefa.titulo }}</h5>
              <p>{{ tarefa.descricao }}</p>
              <p><b>Data de criação:</b> {{ tarefa.criadoEm }}</p>
              <p><b>Data de conclusão:</b> {{ tarefa.concluidaEm || 'Ainda não concluída' }}</p>
              <p>
                <b>Status:</b>
                <q-chip :color="obterCorStatus(tarefa.status)" outline class="q-mt-xs">
                  {{ tarefa.status }}
                </q-chip>
              </p>
            </div>
          </q-item-section>
          <q-item-section side>
            <div class="row items-center no-wrap">
              <!-- Botões de editar, marcar como concluída e excluir -->
              <q-btn
                flat
                round
                icon="edit"
                color="primary"
                class="q-mr-sm"
                :disabled="tarefa.status === 'Concluída'"
                @click="editarTarefa(indice)"
              />
              <q-btn
                flat
                round
                icon="done"
                color="green"
                class="q-mr-sm"
                :disabled="tarefa.status === 'Concluída'"
                @click="marcarComoConcluida(indice)"
              />
              <q-btn flat round icon="delete" color="negative" @click="excluirTarefa(indice)" />
            </div>
          </q-item-section>
        </q-item>
      </q-list>
    </q-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      novaTarefa: {
        titulo: '',
        descricao: '',
      },
      tarefas: [],
      filtroStatus: null, // Filtro para mostrar as tarefas por status
      opcoesStatus: [
        { label: 'Todos', value: null },
        { label: 'Pendente', value: 'Pendente' },
        { label: 'Concluída', value: 'Concluída' },
      ],
      ordemOrdenacao: '', // Definir a ordem de exibição das tarefas
      opcoesOrdenacao: [
        { label: 'Mais recente', value: 'desc' },
        { label: 'Mais antigo', value: 'asc' },
      ],
    }
  },
  methods: {
    adicionarTarefa() {
      // Verifica se os campos estão preenchidos antes de adicionar a tarefa
      if (!this.novaTarefa.titulo.trim() || !this.novaTarefa.descricao.trim()) {
        alert('Por favor, preencha todos os campos antes de adicionar a tarefa.')
        return
      }

      const agora = new Date().toLocaleString()
      this.tarefas.push({
        titulo: this.novaTarefa.titulo.trim(),
        descricao: this.novaTarefa.descricao.trim(),
        criadoEm: agora,
        concluidaEm: null,
        status: 'Pendente', // A tarefa começa como "Pendente"
      })
      this.novaTarefa = { titulo: '', descricao: '' } // Limpa os campos após adicionar
    },
    excluirTarefa(indice) {
      // Remove a tarefa pelo índice
      this.tarefas.splice(indice, 1)
    },
    editarTarefa(indice) {
      // Evita editar tarefas já concluídas
      if (this.tarefas[indice].status === 'Concluída') {
        alert('Tarefa concluída não pode ser editada.')
        return
      }
      const novoTitulo = prompt('Edite o título da tarefa:', this.tarefas[indice].titulo)
      const novaDescricao = prompt('Edite a descrição da tarefa:', this.tarefas[indice].descricao)
      if (
        novoTitulo !== null &&
        novoTitulo.trim() &&
        novaDescricao !== null &&
        novaDescricao.trim()
      ) {
        this.tarefas[indice].titulo = novoTitulo.trim()
        this.tarefas[indice].descricao = novaDescricao.trim()
      }
    },
    marcarComoConcluida(indice) {
      // Marca a tarefa como concluída e define a data de conclusão
      const agora = new Date().toLocaleString()
      this.tarefas[indice].status = 'Concluída'
      this.tarefas[indice].concluidaEm = agora
    },
    obterCorStatus(status) {
      // Define a cor do status da tarefa
      switch (status) {
        case 'Pendente':
          return 'orange'
        case 'Concluída':
          return 'green'
        default:
          return 'grey'
      }
    },
  },
}
</script>

<style scoped>
/* Estilização para card e logo */
.q-card {
  border-radius: 8px;
}

.fundo {
  background-color: black;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  position: relative;
}

.image-container {
  position: absolute;
  top: 10%;
  left: 50%;
  transform: translateX(-50%);
}

.logo {
  width: 150px;
}
</style>
