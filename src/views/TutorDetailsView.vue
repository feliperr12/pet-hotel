<script setup>
import { onMounted, ref } from 'vue';
import { useRoute, RouterLink } from 'vue-router';

const rota = useRoute();
const API_URL = 'http://localhost:3000';

const tutorId = rota.params.id;
const tutor = ref({});
const meusPets = ref([]);

const nomePet = ref('');
const especiePet = ref('');

async function carregarTudoDoTutor() {
  const respostaTutor = await fetch(`${API_URL}/tutores/${tutorId}`);
  tutor.value = await respostaTutor.json();

  const respostaPets = await fetch(`${API_URL}/pets?tutorId=${tutorId}`);
  meusPets.value = await respostaPets.json();
}

async function cadastrarPetVinculado() {
  if (!nomePet.value || !especiePet.value) {
    alert('Preencha nome e espécie do pet!');
    return;
  }

  const petNovo = {
    nome: nomePet.value,
    especie: especiePet.value,
    tutorId: tutorId
  };

  await fetch(`${API_URL}/pets`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(petNovo),
  });

  nomePet.value = '';
  especiePet.value = '';
  carregarTudoDoTutor();
}

onMounted(() => {
  carregarTudoDoTutor();
});
</script>

<template>
  <div>
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h1 class="text-2xl font-bold">Detalhes do Tutor</h1>
      <RouterLink class="btn btn-outline-secondary" to="/tutores">
        Voltar para Tutores
      </RouterLink>
    </div>

    <!-- Info Tutor (Cidade novamente incluída) -->
    <div class="card mb-4 p-3">
      <h3>Informações do Tutor</h3>
      <div class="row mt-2">
        <div class="col-md-4"><strong>ID:</strong> {{ tutor.id }}</div>
        <div class="col-md-4"><strong>Nome Completo:</strong> {{ tutor.nome }}</div>
        <div class="col-md-4"><strong>CPF/CNPJ:</strong> {{ tutor.cpfCnpj || '---' }}</div>
        <div class="col-md-4 mt-2"><strong>Telefone:</strong> {{ tutor.telefone }}</div>
        <div class="col-md-4 mt-2"><strong>E-mail:</strong> {{ tutor.email || '---' }}</div>
        <div class="col-md-4 mt-2"><strong>Nascimento:</strong> {{ tutor.dataNascimento || '---' }}</div>
        <div class="col-md-4 mt-2"><strong>Cidade:</strong> {{ tutor.cidade || '---' }}</div>
      </div>
    </div>

    <!-- Seção de Pets e Cadastro (Sem o contador de pets) -->
    <div class="row">
      <div class="col-md-7">
        <h3>Pets do Tutor</h3>

        <table class="table table-striped mt-3" v-if="meusPets.length > 0">
          <thead>
            <tr>
              <th>ID</th>
              <th>Nome</th>
              <th>Espécie</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="pet in meusPets" :key="pet.id">
              <td>{{ pet.id }}</td>
              <td>{{ pet.nome }}</td>
              <td>{{ pet.especie }}</td>
              <td>
                <RouterLink class="btn btn-sm btn-primary" :to="`/pets/${pet.id}`">
                  Visualizar
                </RouterLink>
              </td>
            </tr>
          </tbody>
        </table>

        <p v-else class="text-muted mt-3">Nenhum pet vinculado a este tutor.</p>
      </div>

      <div class="col-md-5">
        <div class="card p-3">
          <h3>Cadastrar novo pet</h3>
          <p class="text-muted small">O pet será vinculado automaticamente a {{ tutor.nome }}.</p>

          <div class="mb-3">
            <label class="form-label">Nome do pet</label>
            <input type="text" class="form-control" v-model="nomePet" placeholder="Digite o nome do pet" />
          </div>

          <div class="mb-3">
            <label class="form-label">Espécie</label>
            <select class="form-select" v-model="especiePet">
              <option value="" disabled>Selecione</option>
              <option value="Cachorro">Cachorro</option>
              <option value="Gato">Gato</option>
              <option value="Outro">Outro</option>
            </select>
          </div>

          <button class="btn btn-success" @click="cadastrarPetVinculado">Cadastrar pet</button>
        </div>
      </div>
    </div>
  </div>
</template>
