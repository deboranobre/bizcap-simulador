<template>
    <div>
        <b-alert :show="showSuccessBox" dismissible variant="success" class="mt-3">Seus dados foram enviados com sucesso!</b-alert>
        <b-alert :show="showWarningBox" dismissible variant="warning">Ocorreu algum erro. Por favor tente novamente ou entre em contato com a Bizcap.</b-alert>

        <form v-if="showLoanForm" v-on:submit.prevent="onSubmitLoan">
            <b-container fluid>
                <b-row class="mt-3">
                    <label for="loanValue">Valor do Empréstimo</label>
                    <money v-model="loanValue" v-bind="money" class="form-control"></money>
                    <small class="form-text text-muted">Valor máximo de R$100.000,00</small>
                </b-row>

                <b-row class="mt-3">
                    <label for="installmentsNumber">Número de Parcelas</label>
                </b-row>

                <b-row>
                    <vue-slider v-model="installmentsNumber" width="100%" :min='3' :max='12' ></vue-slider>
                </b-row>

                <b-row class="mt-3"> 
                    <p class="text-info m-0"><strong>Taxa de Juros</strong> de 3% a.m. a 8% a.m.*</p>
                </b-row>

                <b-row class="mt-1"> 
                    <p class="text-info m-0">De R$ {{ installmentMin }} a R$ {{ installmentMax }}</p>
                </b-row>

                <b-row class="mt-3">
                    <b-button type="submit" variant="info">Solicitar Empréstimo</b-button>
                </b-row>
            </b-container>
        </form>

        <form v-if="showEmailForm" v-on:submit.prevent="onSubmitEmail">
            <b-container fluid>
                <b-row class="my-3">
                    <label for="loanValue">CNPJ</label>
                    <the-mask v-model="cnpj" :mask="['##.###.###/####-##']" placeholder="Ex: 27.865.757/0063-05" class="form-control"/>
                </b-row>

                <b-row>
                    <label for="email">E-mail</label>
                    <b-form-input v-model="email" type="email" placeholder="bizcap@bizcap.com.br"></b-form-input>
                </b-row>

                <b-row class="mt-3">
                    <b-button type="submit" variant="info">Enviar</b-button>
                </b-row>
            </b-container>
        </form>
    </div>
</template>

<script>
import {Money} from 'v-money'
import {TheMask} from 'vue-the-mask'
import vueSlider from 'vue-slider-component'
import axios from 'axios'

export default {
  name: 'Home',
  components: {
    Money,
    TheMask,
    vueSlider
  },
  data () {
    return {
      showSuccessBox: false,
      showWarningBox: false,
      showLoanForm: true,
      showEmailForm: false,
      loanValue: 5000,
      installmentsNumber: 12,
      installmentsValue: 0,
      installmentMin: 502.31,
      installmentMax: 663.48,
      interest: 0,
      showInfo: false,
      cnpj: '',
      email: '',
      money: {
        decimal: ',',
        thousands: '.',
        prefix: 'R$ ',
        precision: 2,
        masked: false
      }
    }
  },
  methods: {
    setinstallmentsValue: function () {
      this.installmentMin = (this.loanValue * ((Math.pow((1 + 0.03), this.installmentsNumber) * 0.03) / (Math.pow((1 + 0.03), this.installmentsNumber) - 1))).toFixed(2)
      this.installmentMax = (this.loanValue * ((Math.pow((1 + 0.08), this.installmentsNumber) * 0.08) / (Math.pow((1 + 0.08), this.installmentsNumber) - 1))).toFixed(2)
    },
    onSubmitLoan: function () {
      axios({ method: 'POST', 'url': 'https://httpbin.org/post', 'data': {loanValue: this.loanValue, installmentsNumber: this.installmentsNumber} }).then(result => {
        this.showLoanForm = false
        this.showEmailForm = true
      }, error => {
        this.showWarningBox = false
        console.log(error)
      })
    },
    onSubmitEmail: function () {
      axios({ method: 'POST', 'url': 'https://httpbin.org/post', 'data': {cnpj: this.cnpj, email: this.email} }).then(result => {
        this.showSuccessBox = true
        console.log('oooooo')
      }, error => {
        this.showWarningBox = false
        console.log(error)
      })
    }
  },
  watch: {
    installmentsNumber: function () {
      this.setinstallmentsValue()
    },
    loanValue: function (val, oldVal) {
      if (val > 100000) {
        this.loanValue = 5000
      }
      this.setinstallmentsValue()
    }
  }
}
</script>