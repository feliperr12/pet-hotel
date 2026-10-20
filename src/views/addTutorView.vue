<script setup>
import { ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const roteador = useRouter();
const API_URL = 'http://localhost:3000';

const tutor = ref({
  nome: '',
  telefone: '',
  cpfCnpj: '',
  email: '',
  dataNascimento: '',
  rua: '',
  numero: '',
  bairro: '',
  cidade: '',
  estado: '',
  cep: ''
});

const erroNome = ref(false);
const erroTelefone = ref(false);

async function salvarTutor() {
  erroNome.value = !tutor.value.nome;
  erroTelefone.value = !tutor.value.telefone;

  if (erroNome.value || erroTelefone.value) {
    return;
  }

  await fetch(`${API_URL}/tutores`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(tutor.value),
  });

  roteador.push('/tutores');
}
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Cadastrar Tutor</h1>
      <p class="text-body-secondary mb-0">Preencha os dados para cadastrar um novo tutor.</p>
    </header>

    <RouterLink class="btn btn-outline-primary mb-3" to="/tutores">
      Voltar
    </RouterLink>

    <form @submit.prevent="salvarTutor" class="row g-3">
      <h3>Dados do Tutor</h3>

      <div class="col-md-6">
        <label class="form-label">ID</label>
        <input type="text" class="form-control" disabled placeholder="Gerado automaticamente" />
      </div>

      <div class="col-md-6">
        <label class="form-label">Nome Completo</label>
        <input type="text" class="form-control" v-model="tutor.nome" placeholder="Digite o nome completo" />
        <div v-if="erroNome" class="text-danger mt-1">Ops, campo obrigatório, por favor preencha-o!</div>
      </div>

      <div class="col-md-6">
        <label class="form-label">CPF / CNPJ</label>
        <input type="text" class="form-control" v-model="tutor.cpfCnpj" placeholder="Digite o CPF ou CNPJ" />
      </div>

      <div class="col-md-6">
        <label class="form-label">Telefone / Celular / WhatsApp</label>
        <input type="text" class="form-control" v-model="tutor.telefone" placeholder="(00) 00000-0000" />
        <div v-if="erroTelefone" class="text-danger mt-1">Ops, campo obrigatório, por favor preencha-o!</div>
      </div>

      <div class="col-md-6">
        <label class="form-label">E-mail</label>
        <input type="text" class="form-control" v-model="tutor.email" placeholder="nome@example.com" />
      </div>

      <div class="col-md-6">
        <label class="form-label">Data de Nascimento</label>
        <input type="date" class="form-control" v-model="tutor.dataNascimento" />
      </div>

      <h3 class="mt-4">Endereço</h3>
      <div class="col-md-8">
        <label class="form-label">Rua</label>
        <input type="text" class="form-control" v-model="tutor.rua" placeholder="Digite o nome da rua" />
      </div>
      <div class="col-md-4">
        <label class="form-label">Número</label>
        <input type="text" class="form-control" v-model="tutor.numero" placeholder="Número" />
      </div>
      <div class="col-md-6">
        <label class="form-label">Bairro</label>
        <input type="text" class="form-control" v-model="tutor.bairro" placeholder="Digite o bairro" />
      </div>
      <div class="col-md-6">
        <label class="form-label">Cidade</label>
        <input type="text" class="form-control" v-model="tutor.cidade" placeholder="Digite a cidade" />
      </div>
      <div class="col-md-6">
        <label class="form-label">Estado</label>
        <select class="form-select" v-model="tutor.estado">
          <option value="" disabled>Selecione</option>
          <option value="SC">SC</option>
          <option value="PR">PR</option>
          <option value="RS">RS</option>
        </select>
      </div>
      <div class="col-md-6">
        <label class="form-label">CEP</label>
        <input type="text" class="form-control" v-model="tutor.cep" placeholder="00000-000" />
      </div>

      <div class="col-12 d-flex gap-2 mt-4">
        <RouterLink class="btn btn-secondary" to="/tutores">Cancelar</RouterLink>
        <button class="btn btn-success" type="submit">Cadastrar Tutor</button>
      </div>
    </form>
  </div>
</template>
