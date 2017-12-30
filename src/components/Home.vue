<template>
    <div>
        <b-alert :show="showSuccessBox" dismissible variant="success" class="mt-3">Seus dados foram enviados com sucesso!</b-alert>
        <b-alert :show="showWarningBox" dismissible variant="warning">Ocorreu algum erro. Por favor tente novamente ou entre em contato com a Bizcap.</b-alert>

        <form v-if="showLoanForm" v-on:submit.prevent="onSubmitLoan">
            <b-container fluid>
                <b-row class="my-3">
                    <label for="loanValue">Valor do Empréstimo</label>
                    <money v-model="loanValue" v-bind="money" class="form-control"></money>
                    <small class="form-text text-muted">Valor máximo de R$100.000,00</small>
                </b-row>

                <b-row>
                    <label for="installmentsNumber">Número de Parcelas</label>
                    <b-form-input v-model="installmentsNumber" type="number" min="3" max="12" v-on:change="onInstallmentsNumberChange()"></b-form-input>
                </b-row>

                <b-row v-if="showInfo"> 
                    <small class="form-t;~´=ext text-muted">Valor da parcela: R$ {{ installmentsValue }} com juros de {{ interest }} a.m.</small>
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
import axios from 'axios'

export default {
  name: 'Home',
  components: {Money, TheMask},
  data () {
    return {
      showSuccessBox: false,
      showWarningBox: false,
      showLoanForm: true,
      showEmailForm: false,
      loanValue: 0,
      installmentsNumber: 3,
      installmentsValue: 0,
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
    onInstallmentsNumberChange: function () {
      axios({ method: 'GET', 'url': 'https://httpbin.org/get' }).then(result => {
        this.setInstallmentValue(this.loanValue, parseInt(this.installmentsNumber))
      }, error => {
        console.error(error)
      })
    },
    setInstallmentValue: function (loanValue, installmentsNumber) {
      switch (installmentsNumber) {
        case 3:
          this.interest = 3
          break
        case 4:
          this.interest = 4
          break
        case 5:
          this.interest = 4.5
          break
        case 6:
          this.interest = 5
          break
        case 7:
          this.interest = 5.5
          break
        case 8:
          this.interest = 6
          break
        case 9:
          this.interest = 6.5
          break
        case 10:
          this.interest = 7
          break
        case 11:
          this.interest = 7.5
          break
        case 12:
          this.interest = 8
          break
      }

      let interestValue = (this.loanValue / this.installmentsNumber) * (this.interest / 100)
      this.installmentsValue = ((this.loanValue / this.installmentsNumber) + interestValue).toFixed(2)

      this.showInfo = true
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
  }
}
</script>