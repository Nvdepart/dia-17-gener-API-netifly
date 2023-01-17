<template>
  <v-container>
    <v-row>
      <v-col>
          <h1 style="font-size:10vh">{{ultimMissatge}}</h1>  
          <h4>{{ ultimUsuari }}</h4>
       <!--<v-btn @click="descarregarUltimText()">ultim text</v-btn>-->
      </v-col>
      <v-col>
        <v-text-field
          outlined
          rounded
          clearable
          label="Missatge"
          v-model="missatge"
          type="text"
          prepend-inner-icon="mdi-email"
          maxlength="120"
          single-line
          counter
          style="max-width:50%"
        ></v-text-field>
        <v-text-field
          outlined
          rounded
          clearable
          label="Usuari"
          v-model="usuari"
          type="text"
          prepend-inner-icon="mdi-account"
          style="max-width:50%"
        ></v-text-field>
        <v-btn @click="enviarMissatge(missatge, usuari)">ENVIAR</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col style="background-color:aquamarine" class="pa-md-5 mr-md-1 " cols="3" v-for="(m,i) in els20Missatges">
       
        <p>Missatge es <strong>{{m.missatge}}</strong></p>
        <p><strong>el usuari es {{m.usuari}}</strong></p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
export default {
  data(){
    return{
      respostaTest:null,
      ultimMissatge:"",
      ultimUsuari:"",
      missatge:"",
      usuari:"",
      els20Missatges:[]
    }
  },
  methods:{
    testejarApi(){
      console.log('Provant api..')
      let self = this
      this.$axios.get('https://last-one-smo-4w3qk.ondigitalocean.app/api')
        // Quan acabi
        .then(
          resposta=>{
            console.log("M'he descarregat les dades bé",resposta.data)
            self.respostaTest = resposta
            
          }
        )
        // Si hi ha errors
        .catch(
          error=>{
            console.log("M'he descarregat les dades malament",error)
          }
        )
    },
    descarregarTexts(){
      let self = this
      this.$axios.get('https://last-one-smo-4w3qk.ondigitalocean.app/api/getTexts')
      .then(
        resp=>{
          console.log('Ultim 20 texts',resp)
          // Recollim
          let dadesTots = resp.data.data.missatges
          //let usuari = resp.data.data.usuari
          // Assignem al data
          self.els20Missatges = dadesTots
          
        }
      )
      .catch(
        e=>{
          console.log('Ultim text error',e)
        }
      )
    },
    descarregarUltimText(){
      let self = this
      this.$axios.get('https://last-one-smo-4w3qk.ondigitalocean.app/api/getText')
      .then(
        r=>{
          console.log('Ultim text',r)
          // Recollim
          let missatge = r.data.data.missatge
          let usuari = r.data.data.usuari
          // Assignem al data
          self.ultimMissatge = missatge
          self.ultimUsuari = usuari
        }
      )
      .catch(
        e=>{
          console.log('Ultim text error',e)
        }
      )
    },
    enviarMissatge(){
      let self = this
      let dades = {
          missatge: self.missatge,
          usuari: self.usuari
      }
      let url="https://last-one-smo-4w3qk.ondigitalocean.app/api/setText"

      this.$axios.post(url,dades)
      .then(
        res=>{
          console.log("S'ha enviat bé");
          self.missatge=""
          self.descarregarUltimText = enviarMissatge
        }
      )
      .catch(
        er => {
          console.log("No s'ha enviat bé");
        }
      )
    }
  },
  mounted(){
    var self = this
    setInterval(function(){
      self.descarregarUltimText()
      self.descarregarTexts()
    }, 2000

    );
    // console.log("Abans de descarregar");
    // this.descarregarUltimText()
    // console.log("despres de descarregar")
  }
}
</script>
