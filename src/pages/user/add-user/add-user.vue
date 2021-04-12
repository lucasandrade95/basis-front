<template>
  <q-page class="justify-center">
    <div class="section-page bg-white q-mt-lg"
         style="margin:auto; max-width:650px; border-radius: 10px">
      <q-toolbar class="bg-white text-primary items-center" style="border-radius: 10px">
        <q-toolbar-title class="text-h6">Cadastro de Usuário</q-toolbar-title>
        <q-btn flat round icon="las la-times" @click="$router.back()"></q-btn>
      </q-toolbar>
      <form class="q-gutter-sm q-pa-md">
        <q-input
          rounded outlined
          color="primary" type="text"
          :error="formerro.name.length > 0"
          :rules=" [val => val !== null && val !== '' || 'Digite seu nome']"
          :error-message="formerro.name"
          v-model="form.name" label="Nome Completo">
          <template v-slot:prepend>
            <q-icon name="las la-user-alt" />
          </template>
        </q-input>

        <q-input
          rounded outlined
          color="primary"
          :error-message="formerro.telephone"
          type="text" :error="formerro.telephone.length > 0"
          v-model="form.telephone" label="Telefone"
          :mask="form.telephone.length > 14?'(##) #####-####':'(##) ####-#####'">
          <template v-slot:prepend>
            <q-icon name="las la-mobile-alt" />
          </template>
        </q-input>

        <div class="text-subtitle-2 text-primary text-weight-bold">Informações de Endereço
          <span class="text-weight-light">(Opcional)</span>
        </div>

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.cep"
          :error="formerro.cep.length>0"
          type="text"
          :loading="ceploading"
          v-model="form.cep"
          label="CEP"
          mask="#####-###"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.endereco"
          :error="formerro.endereco.length>0"
          type="text"
          v-model="form.endereco"
          label="Endereço"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.numero"
          :error="formerro.numero.length>0"
          type="text"
          v-model="form.numero"
          label="Número"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          type="text"
          v-model="form.complemento"
          label="Complemento"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.bairro"
          :error="formerro.bairro.length>0"
          type="text"
          v-model="form.bairro"
          label="Bairro"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.cidade"
          :error="formerro.cidade.length>0"
          type="text"
          v-model="form.cidade"
          label="Cidade"
        />

        <q-input
          rounded
          outlined
          color="primary"
          hide-bottom-space
          :error-message="formerro.estado"
          :error="formerro.estado.length>0"
          type="text"
          v-model="form.estado"
          label="Estado"
        />

        <div class="text-subtitle1 text-primary text-weight-bold">Dados para Acesso</div>

        <q-input
          rounded outlined
          color="primary"
          type="email"
          :error="formerro.email.length > 0"
          v-model="form.email" label="E-mail"
          :error-message="formerro.email"
          :rules="[ val => val && val.length > 0 || 'Digite seu e-mail',
          v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail inválido']">
          <template v-slot:prepend>
            <q-icon name="las la-envelope" />
          </template>
        </q-input>

        <q-input
          class="q-mb-sm"
          :rules=" [val => val !== null && val !== '' || 'Digite sua senha']"
          outlined rounded bg-color="white" :type="isPwd ? 'password' : 'text'" v-model="form.password"
          label="Senha" :error="formerro.password.length > 0" :error-message="formerro.password">
          <template v-slot:prepend>
            <q-icon name="las la-lock" />
          </template>
          <template v-slot:append>
            <q-icon class="cursor-pointer" :name="'las la-'+(isPwd ? 'eye-slash' : 'eye')" @click="isPwd = !isPwd" />
          </template>
        </q-input>

      </form>
      <div class="q-pa-md">
        <q-btn outline rounded unelevated size="lg" class="full-width" icon-right="las la-user-check" color="primary" @click.native="envia()" label="Enviar Dados" />
      </div>
    </div>
  </q-page>
</template>

<script>
/* eslint-disable */
import {
  QToggle,
  QBtn,
  QCard,
  QCardActions,
  QField,
  QInput,
  QTabs,
  QTab,
  Loading,
  Notify
} from 'quasar'
import {mapMutations} from 'vuex'
import axios from 'axios'

export default {
  components: {
    QToggle,
    QBtn,
    QCardActions,
    QCard,
    QField,
    QInput,
    Loading,
    QTabs,
    QTab
  },
  data() {
    const today = new Date();
    let year = today.getFullYear() - 30;
    year = '' + year;
    return {
      isPwd: true,
      enviado: false,
      defaultYear: year,
      ceploading: false,
      form: {
        name: '',
        telephone: '',
        email: '',
        password: '',
        cep: '',
        endereco: '',
        numero: '',
        complemento: '',
        bairro: '',
        cidade: '',
        estado: '',
      },
      formerro: {
        name: '',
        telephone: '',
        email: '',
        password: '',
        cep: '',
        endereco: '',
        numero: '',
        complemento: '',
        bairro: '',
        cidade: '',
        estado: '',
      },
      dialog:true,
      maximizedToggle:true,
    }
  },
  watch: {
    'form.cep': function () {
      var comp = this
      var cep = comp.form.cep.replace(/[^0-9]/g, '');
      if (cep.length === 8) {
        comp.ceploading = true
        // 'http://cep.correiocontrol.com.br/' + cep + '.json'
        // 'http://api.postmon.com.br/v1/cep/' + cep + ''
        const instance = axios.create()
        instance.defaults.headers.common = {}
        instance.get('https://viacep.com.br/ws/' + cep + '/json')
          .then(function (response) {
            comp.form.endereco = response.data.logradouro
            comp.form.cidade = response.data.localidade
            comp.form.estado = response.data.uf
            comp.form.bairro = response.data.bairro
            console.log(response);
            comp.ceploading = false
          })
          .catch(function (error) {
            console.log(error);
            comp.ceploading = false
          });
      } else {
        return false;
      }
    }
  },
  methods: {
    envia() {
      var comp = this
      Loading.show()
      const data = {
        cep: this.form.cep,
        endereco: this.form.endereco,
        numero: this.form.numero,
        complemento: this.form.complemento,
        bairro: this.form.bairro,
        cidade: this.form.cidade,
        estado: this.form.estado,
        name: this.form.name,
        email: this.form.email,
        password: this.form.password,
        telephone: this.form.telephone,
      }

      if (this.form.password === '' || this.form.email === '' || this.form.name === '' || this.form.nasc === '') {
        Loading.hide()
        Notify.create({
          message: 'Preencha os campos!',
          icon: 'warning',
          color: 'orange',
          actions: [{ icon: 'close', color: 'white' }]
        })
      } else {
        axios.post('http://localhost:8000/users-basis', data).then((response) => {
          debugger;
          Loading.hide()
              Notify.create({
                message: 'Usuário Cadastrado!',
                icon: 'warning',
                color: 'positive',
                actions: [{icon: 'close', color: 'white'}]
              })
          comp.enviado = true
          comp.$router.push('/');
        })
      }
    },
  }
}
</script>
