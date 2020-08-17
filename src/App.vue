<template>
  <div id="app">


    <div class="alert" v-if="loading = !loading">Loading</div>
   <h1 class="text-center">KRAKEN API</h1>
     <section>
       <div class="container">
         <table>
          <tbody>

            <tr>
              <td v-for="field in fields"><strong>{{field}}</strong></td>
            </tr>
            <tr v-for="item in items">
              <td>{{item.pair}}</td>
              <td>{{item.bid}}</td>
              <td>{{item.ask}}</td>
            </tr>
          </tbody>
        </table>
       </div>
     </section>
      
  </div>
</template>

<script>
import {mapState} from 'vuex'
import './assets/css/style.css';
import axios from 'axios'

export default {
 data(){
  return{
    url: 'https://api.kraken.com/0/public/Ticker?pair=',
        fields: ['pair', 'bid', 'ask'],
        items: [],
        loading: true,
        currency: {
          XBTUSD: 'XBTUSD',
          XBTEUR: 'XBTEUR',
          XBTCAD: 'XBTCAD',
          XBTJPY: 'XBTJPY',
          XBTGBP: 'XBTGBP'
    },
    interval: 15000
  }
 },
 created() {
      let func = this;
      this.updateCurrency();
        setInterval(function () {
          func.items = [];
          this.loading = false;
          func.updateCurrency();
        }, func.interval)
    },
    methods: {
      updateCurrency: function () {
        let pairsList = '';
        for (let currency in this.currency) {
          pairsList += currency + ',';
        }
        pairsList = pairsList.substring(0, pairsList.length - 1);
        this.loading = false;
        axios
        .get(this.url + pairsList)
            .then(response => {
              
              for (let currency in response.data.result) {
                this.items.push({
                  'pair': currency,
                  'bid': response.data.result[currency].b[0],
                  'ask': response.data.result[currency].a[0]
                });
              }
              this.loading = true;
            })
            .catch(error => console.log(error + error.status))
      }
    }
}
</script>

<style>
table{
  width: 100%;
  text-transform: uppercase;
}
tr:nth-child(odd){
  background-color: #eee;
}
tr:nth-child(even){
  background-color: #fff;
}
td{
  padding: 15px 20px;
  font-size: 18px;
  text-align:center;
}
.alert{
  position: fixed;
  z-index: 100;
  background-color: rgba(0,0,0,.7);
  color: #fff;
  height: 100%;
  width: 100%;
  top: 0;
  left: 0;
  font-size: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>

