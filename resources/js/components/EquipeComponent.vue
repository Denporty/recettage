<template>
    <div class="container">
        <div class="form-row mt-5" style="max-width: 50%; margin: auto;">
            <input type="text" class="form-control" @keyup="searchEquipe" v-model="q" placeholder="Rechercher une équipe...">
            </div>

<div  v-for="equipe in equipes.data" :key="equipe.id" class="modal fade" id="deleteModal" tabindex="-1" aria-labelledby="deleteModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="deleteModalLabel">Modal title</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <p style="color:red;">Attention si vous supprimez une équipe les matchs dont elle fait partie seront automatiquement supprimés.</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Fermer</button>
        <button type="button" class="btn btn-danger" data-dismiss="modal" @click="deleteEquipe(equipe.id)">Supprimer</button>
      </div>
    </div>
  </div>
</div>

        <add-equipe @equipe-added="refresh"></add-equipe>
            <ul class="list-group">
                <li class="list-group-item d-flex justify-content-between align-items-center" v-for="equipe in equipes.data" :key="equipe.id">
                        <a href="#">{{ equipe.name }}</a>
                        <p> {{ equipe.nombre_de_joueurs }}</p>
                        <div>
                        <button type="button" class="btn btn-secondary" data-toggle="modal" data-target="#editModal" @click="getEquipe(equipe.id)">
                            Editer
                        </button>
                        <button type="button" class="btn btn-danger" data-toggle="modal" data-target="#deleteModal">
                            Supprimer
                        </button>
                        </div>
                    </li>
                    <edit-equipe v-bind:equipeToEdit="equipeToEdit" @equipe-updated="refresh"></edit-equipe>
                </ul>
                <pagination :data="equipes" @pagination-change-page="getResults" class="mt-5"></pagination>
    </div>
</template>

<script>
    export default {

        data(){
            return {
                equipes: {},
                equipeToEdit: '',
                q: ''
            }
        },

        created(){
            axios.get('http://127.0.0.1:8000/api/equipesList')
                .then(response => this.equipes = response.data)
                .catch(error => console.log(error));
        },

        methods: {
            getResults(page = 1) {
			axios.get('http://127.0.0.1:8000/api/equipesList?page=' + page)
				.then(response => {
					this.equipes = response.data;
				});
        },  

        getEquipe(id) {
            axios.get('http://127.0.0.1:8000/api/equipes/edit/' + id)
            .then(response => this.equipeToEdit = response.data)
            .catch(error => console.log(error));
        },

        deleteEquipe(id){
            axios.delete('http://127.0.0.1:8000/api/equipes/' + id)
            .then(response => this.equipes = response.data)
            .catch(error => console.log(error));
        },

        searchEquipe() {
            if(this.q.length > 3){
            axios.get('http://127.0.0.1:8000/api/equipesList/' + this.q)
                .then(response => this.equipes = response.data)
                .catch(error => console.log(error));
            } else {
            axios.get('http://127.0.0.1:8000/api/equipesList/')
                .then(response => this.equipes = response.data)
                .catch(error => console.log(error));
            }
        },
        
        refresh(equipes) {
            this.equipes = equipes.data
        }
        },

        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
