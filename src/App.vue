<template>
  <div id="app">
    <div class="container">
      <b-alert variant="success" show>KRAKEN API by OWIII</b-alert>
      <div class="table-block" v-if="show">
        <b-table head-variant="light" hover bordered :items="items" :fields="fields"></b-table>
      </div>
      <div class="d-flex justify-content-center spinner-block" v-else>
        <div class="spinner-border text-primary" role="status">
          <span class="sr-only">Loading...</span>
        </div>
      </div>
      <div class="alert alert-primary" role="alert">
        Данные обновляются каждые {{interval}} сек.
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'app',
    components: {},
    data: function () {
      return {
        url: 'https://api.kraken.com/0/public/Ticker?pair=',
        fields: ['pair', 'bid', 'ask'],
        items: [],
        show: true,
        currency: {
          XBTUSD: 'XBTUSD',
          XBTEUR: 'XBTEUR',
          XBTCAD: 'XBTCAD',
          XBTJPY: 'XBTJPY',
          XBTGBP: 'XBTGBP'
        },
        interval: 15,
      }
    },
    created() {
      let func = this;
      this.updateCurrency();
        setInterval(function () {
          func.items = [];
          this.show = false;
          func.updateCurrency();
        }, func.interval * 1000)
    },
    methods: {
      updateCurrency: function () {
        let pairsList = '';
        for (let currency in this.currency) {
          pairsList += currency + ',';
        }

        pairsList = pairsList.substring(0, pairsList.length - 1);
        this.show = false;
        axios.get(this.url + pairsList)
            .then(response => {
              for (let currency in response.data.result) {
                this.items.push({
                  'pair': currency,
                  'bid': response.data.result[currency].b[0],
                  'ask': response.data.result[currency].a[0]
                });
              }
              this.show = true;
            })
            .catch(error => console.log(error + error.status))
      }
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    text-align: center;
    color: #2c3e50;
    max-height: 100vh;
    height: 100vh;
  }

  .table-block {
    min-height: 300px;
    margin-top: 60px;
    font-size: 12px;
    border-radius: 20px;
  }

  .spinner-block {
    margin-top: 60px;
    align-items: center;
    min-height: 300px;
  }

  .alert {
    margin-top: 30px;
  }

  .my-input {
    display: block;
    min-width: 280px;
    padding-left: 10px;
  }
</style>
