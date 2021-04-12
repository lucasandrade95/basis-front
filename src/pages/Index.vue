<template>
  <q-page class="justify-center">
    <div class="section-page bg-white q-mt-lg"
         style="margin:auto; max-width:650px; border-radius: 10px">
    <q-card>
      <q-toolbar class="bg-white text-primary items-center" style="border-radius: 10px">
        <q-toolbar-title class="text-h6">Cadastro de Usuário</q-toolbar-title>
      </q-toolbar>
      <q-btn
        outline
        rounded size="md"
        color="primary" class="flex-center" title="Adicionar usuário" to="/user/addUser"
        style="float: right;margin-right: 40px;">
        <q-icon name="las la-plus-circle" />
      </q-btn>
      <table class="q-pa-lg">
        <thead>
        <tr>
          <th class="text-left q-pa-sm">Nome</th>
          <th class="text-left q-pa-sm">Email</th>
          <th class="text-left q-pa-sm">Cep</th>
          <th class="text-left q-pa-sm">Cidade</th>
          <th class="text-left q-pa-sm">Estado</th>
        </tr>
        </thead>
        <tbody v-for="(item,i) in users" :key="'i'+i">
        <tr>
          <td class="q-pa-sm">{{item.name}}</td>
          <td class="q-pa-sm">{{item.email}}</td>
          <td class="q-pa-sm">{{item.cep}}</td>
          <td class="q-pa-sm">{{item.cidade}}</td>
          <td class="q-pa-sm">{{item.estado}}</td>
        </tr>
        </tbody>
      </table>
    </q-card>
    </div>
  </q-page>
</template>

<script>
import axios from "axios";
export default {
  data () {
    return {
      tab: 'home',
      users: []
    }
  },
  mounted() {
    this.getUsers();
  },
  methods: {
    getUsers () {
      const t = this;
      axios.get('http://localhost:8000/users-basis').then((response) => {
        t.users = response.data;
      })
    }
  }
}
</script>
