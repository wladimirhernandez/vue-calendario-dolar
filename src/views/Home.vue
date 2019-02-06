<template>
  <v-layout :wrap="true">
    <v-flex xs12>
      <v-card>
          <v-date-picker 
          v-model="date"
          full-width
          locale="es-cl"
          color="accent"
          :min="min"
          :max="max"
          @change="getDolar(date)"
          ></v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-xs-center"> {{ valor }}</v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from 'axios'
import { mapMutations } from 'vuex'

  export default {
    components: {
      
    },
    data() {
      return {
        date: new Date().toISOString().substr(0, 10),
        min: '1984',
        max: new Date().toISOString().substr(0, 10),
        valor: null
      }
    },
    methods: {

      ...mapMutations(['showLoading','hideLoading']),

      async getDolar(day){
        let arrayFecha = day.split(['-'])
        let ddmmyy = arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0]
       
        try {
          this.showLoading({title:'Accediendo a información', color:'secondary'})
         let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)

          if(datos.data.serie.length > 0) {
            this.valor = await datos.data.serie[0].valor
          } else {
            this.valor = 'Sin Resultados'
          }
         
        } catch(error) {
          //console.log(error)
        }
        finally{
          this.hideLoading()
        }
      }
    },
    created(){
      this.getDolar(this.date)
    }
  }
</script>
