<script setup>
import {doAjaxRequest} from "@/api";
import {onMounted, reactive} from "vue";
let num = 0;
let d= false;
let data = reactive({
  listProduits: [],
  totalPages: 0
});
function afficherProduit(){
  const fetchOptions = { method: "GET" };
  doAjaxRequest("/api/produits?page=" + num + "&size=5&sort=nom,asc", fetchOptions)
      .then((dataJSON) => {
        data.listProduits = dataJSON._embedded.produits;
        data.totalPages = dataJSON.page.totalPages;
      })
      .catch((error) => {
        console.log(error);
      });
}

function debut(){
  if(num !== 0){
    num = 0
    afficherProduit()
  }
}
function fin(){
  if(num !== data.totalPages - 1){
    num = data.totalPages - 1
    afficherProduit()
  }
}
function suivante(){
  if(num < data.totalPages - 1){
    num = num +1
    afficherProduit()
  }
}

function precedente(){
  if(num!=0){
    num = num -1
    afficherProduit()
  }
}

onMounted(afficherProduit);

</script>
<template>
  <div>
    <table>
      <caption>Les produits - page {{num +1}}/{{data.totalPages}}</caption>
      <tr>
        <th>Nom</th>
        <th>Prix</th>
        <th>Stock</th>
        <th>Commandés</th>
      </tr>
      <tr v-if="data.listProduits.length === 0">
        <td colspan="4">Veuillez patienter, chargement des produits...</td>
      </tr>
      <tr v-for="p in data.listProduits" :key="p.reference">
        <td>{{ p.nom }}</td>
        <td>{{ p.prixUnitaire }}</td>
        <td>{{ p.unitesEnStock }}</td>
        <td>{{ p.unitesCommandees }}</td>
      </tr>
    </table>
      <table width="610px">
      <tr>
        <td>
        <button @click="debut()" :disabled="num === 0">
          ⇆
        </button>
          </td>
        <td>
        <button @click="precedente()" :disabled="num === 0">
          ←
        </button>
        </td>
        <td>
        <button @click="suivante()" :disabled="num >= data.totalPages - 1">
          →
        </button>
          </td>
        <td>
        <button @click="fin()" :disabled="num >= data.totalPages - 1">
          ⇄
        </button>
        </td>
      </tr>

    </table>
  </div>
</template>

<script>
export default {
  name: "ProduitView"
}
</script>


<style scoped>
table{
  text-align: center;
}
td, th {
  border: 1px solid black;
  padding: 8px;
  height: 50px;
}

th {
  width: 300px;
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
