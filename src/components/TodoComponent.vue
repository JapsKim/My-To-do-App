<template>
    <div class="mt-5 container" style="max-width: 800px">
        <div class="text-center">
        <h1> {{AppTitle}}</h1>
        </div>

        <div class="container">
            <div class="w-100 mt-2 d-flex">
                <input type="text" v-model="nouvelleTache.Tache" @change="ajouterTache" class="form-control" placeholder="Add your task">
                <button @click="ajouterTache" class="btn btn-primary">
                    Submit
                </button>
            </div>
        </div>
        <div class="mt-2">
            <table class="table table-bordered">
                <thead>
                    <tr>
                        <th scope="col" class=""></th>
                        <th scope="col" class="">Task</th>
                        <th scope="col" class="">Description</th>
                        <th scope="col" class="">Status</th>
                        <th scope="col" class="">Edit</th>
                        <th scope="col" class="">Delete</th>
                    </tr>
                </thead>
                <tbody v-for="(tache,index) in taches" :key="tache.id">
                    <tr>
                        <td>
                            {{ index+1 }}
                        </td>
                        <td>{{ tache.Tache }}</td>
                        <td> {{ tache.Description }}</td>
                        <td> {{ tache.Status }}</td>
                        <td> 
                            <span class="btn btn-outline-success fa fa-pen" @click="editerTache(index)"></span>
                        </td>
                        <td> 
                            <span class="btn btn-outline-danger fa fa-trash" @click="supprimerTache(index)"></span>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    
</template>


<script setup>


const AppTitle = 'My To Do App';



</script>

<script>

export default {
  data() {
    return {
      nouvelleTache: { 
        Tache: '', 
        Description: '',
        Status: 'To do',
    }
    };
  },
  created() {
  const savedTasks = sessionStorage.getItem('tasks');
  this.taches = savedTasks ? JSON.parse(savedTasks) : [];
 },
 methods: {
  ajouterTache() {
    if (this.nouvelleTache.Tache) {
        this.taches.push({ id: Date.now(), ...this.nouvelleTache });
        this.nouvelleTache.Tache = '';
        this.nouvelleTache.Description = '';
        this.nouvelleTache.status = 'To do';

      // Enregistrer les tâches dans le localStorage
      sessionStorage.setItem('tasks', JSON.stringify(this.taches));
    }
  },
  editerTache(index){
    this.taches = this.taches[index].Tache;
    this.editerTache=index;
  },
  supprimerTache(index) {
    this.taches.value.splice(index, 1);
},
  submitTask(){
    if(this.taches.length===0) return;
    if(this.editerTache.length===0) return;
    if(this.taches.length===0) return;

  }
}
}
</script>
