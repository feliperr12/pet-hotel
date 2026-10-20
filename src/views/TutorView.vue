<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const tutores = ref([]);
const pets = ref([]);
const API_URL = 'http://localhost:3000';

async function carregarTudo() {
  const respostaTutor = await fetch(`${API_URL}/tutores`);
  tutores.value = await respostaTutor.json();

  const respostaPet = await fetch(`${API_URL}/pets`);
  pets.value = await respostaPet.json();
}

function contarPets(tutorId) {
  let contador = 0;
  for (let i = 0; i < pets.value.length; i++) {
    if (String(pets.value[i].tutorId) === String(tutorId)) {
      contador++;
    }
  }
  return contador;
}

function listarNomesPets(tutorId) {
  let meusPets = [];
  for (let pet of pets.value) {
    if (String(pet.tutorId) === String(tutorId)) {
      meusPets.push(pet.nome);
    }
  }
  if (meusPets.length === 0) {
    return 'Nenhum pet vinculado';
  }
  return meusPets.join(' / ');
}

async function excluirTutor(id) {
  let temPet = false;
  for (let p of pets.value) {
    if (String(p.tutorId) === String(id)) {
      temPet = true;
    }
  }

  if (temPet) {
    alert('Ops! Este tutor tem pets vinculados, remova os pets primeiro.');
    return;
  }

  const confirmado = window.confirm('Deseja realmente remover este tutor?');
  if (confirmado) {
    await fetch(`${API_URL}/tutores/${id}`, {
      method: 'DELETE',
    });
    carregarTudo();
  }
}

onMounted(() => {
  carregarTudo();
});
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Tutores</h1>
      <p class="text-body-secondary mb-0">Consulte os tutores cadastrados e seus pets.</p>
    </header>

    <RouterLink class="btn btn-outline-primary mb-3" to="/tutores/novo">
      Cadastrar Tutor
    </RouterLink>

    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome Completo</th>
          <th>CPF / CNPJ</th>
          <th>Telefone / Celular / WhatsApp</th>
          <th>E-mail</th>
          <th>Pets (Quantidade)</th>
          <th>Nomes dos Pets</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="tutor in tutores" :key="tutor.id">
          <td>{{ tutor.id }}</td>
          <td>{{ tutor.nome }}</td>
          <td>{{ tutor.cpfCnpj || '***.***.***-**' }}</td>
          <td>{{ tutor.telefone }}</td>
          <td>{{ tutor.email || '-' }}</td>
          <td>{{ contarPets(tutor.id) }}</td>
          <td>{{ listarNomesPets(tutor.id) }}</td>
          <td>
            <RouterLink class="btn btn-primary btn-sm me-2" :to="`/tutores/${tutor.id}`">
              Visualizar
            </RouterLink>
            <button class="btn btn-danger btn-sm" @click="excluirTutor(tutor.id)">
              Remover
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
