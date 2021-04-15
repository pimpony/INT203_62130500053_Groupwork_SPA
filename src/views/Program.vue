<template>
  <div class="flex justify-center items-center bg-cover bg-scroll bg-center h-screen" style="background-image: url(https://www.chloeting.com/splash/bg-img.jpeg)">
    <div class="h-full w-screen flex justify-center items-center">
      <!-- block image to search and can edit , delete  -->
      <div class="h-screen w-3/6 bg-white bg-opacity-50 items-center mx-auto rounded-xl">
        <div class="mt-3">
          <!-- Search -->
          <input
            v-model="inputSearch"
            placeholder="What are you finding?"
            class="p-2 bg-white w-full rounded border"
          />
           <!-- if notfound Show ... -->
          <p v-if="notFound" class="text-center text-xl mt-4">
            We can't find the Program <br />that you're looking for...
          </p>
          <!-- Select Programs show Popup  -->
       <div class=" bg-gray-300 bg-opacity-75 rounded-xl"  >
        <span @click="closeProgram"  class="text-xl bg-pink close-icon text-black flex justify-end mr-5" >
          x
          </span>
          <div class="col-span-2 mt-1 mb-3">
            <div class="grid grid-cols-2 ml-8">
              <div>             
                <h1 class="text-xl font-bold">Programname : {{ currentProgram.name }}</h1>
                <p class="mt-2">Description :<br/> {{ currentProgram.description }}</p>
              </div>
                <div class="flex justify-center items-center">
                  <div class=" grid grid-cols ">
                    <h1 class="bg-gray-400 text-white text-2xl flex justify-center rounded-full items-center my-2 ">
                      {{ currentProgram.price + ".-" }}
                    </h1>
                    
                    <img
                      :src="currentProgram.imgSrc"
                      class="w-32 h-44 flex justify-center items-center"
                    />
        
                  </div>
              </div>
          </div>
        </div> 
      </div>
          

          <!-- Show Programs all  -->
          <div class="max-h-4.5/6 overflow-y-auto">
            <div v-for="m in searchProgram" :key="m.id">
              <Program-block
                :ProgramImg="m.imgSrc"
                :ProgramName="m.name"
                :ProgramPrice="m.price"
                @click="selectedProgram(m)"
                @delete-click="deleteProgram(m.id)"
                @edit-click="openEditModal"
              />
            </div>
          </div>
         
        </div>
      </div>
        
     

    </div>
  </div>

  <modal v-if="addClicked"  @close="changeAddItemClicked" formLabel="Add New Program" @save-Program="addNewProgram" ></modal>
  <modal v-if="editClicked" @close="changeEditItemClicked" formLabel="Edit Program" :ProgramImgFromDb="currentProgram.imgSrc" :name="currentProgram.name"
    :description="currentProgram.description" :price="currentProgram.price" @save-Program="editProgram"></modal>
</template>
<script>
import ProgramBlock from "../components/ProgramBlock";
import Modal from "../components/Modal";

export default {
  emits: ["close-add-modal",],
  props: ["addClicked"],
  components: {
    Modal,
    ProgramBlock,
  },
  data() {
    return {
      url: " http://localhost:3000/Program",
      Program: [],
      inputSearch: "",
      notFound: false,
      currentProgram: [],
      editClicked: false,
      
    };
  },
  methods: {
    changeAddItemClicked(value) {
      this.$emit("close-add-modal", value);
    },
    changeEditItemClicked(value) {
      this.editClicked = !value;
    },
    changeNotFound(value) {
      this.notFound = value;
    },
    async fetchProgram() {
      const res = await fetch(this.url);
      const data = await res.json();
      return data;
    },
    closeProgram() {
      this.currentProgram = false;
    },
    selectedProgram(Program) {
      this.currentProgram = Program;
      
    },
    async addNewProgram(newProgram) {
      const res = await fetch(this.url, {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify({
          name: newProgram.name,
          description: newProgram.description,
          price: newProgram.price,
          imgSrc: newProgram.imgSrc,
        }),
      });
      const data = await res.json();
      this.Program = [...this.Program, data];
      this.currentProgram = this.Program[this.Program.length - 1];
    },
    async deleteProgram(id) {
      const res = await fetch(`${this.url}/${id}`, {
        method: "DELETE",
      });
      res.status === 200
        ? (this.Program = this.Program.filter((m) => m.id !== id))
        : alert("Error to delete Program");
      this.currentProgram = this.Program[this.Program.length - 1];
    },
    openEditModal(value) {
      this.editClicked = value;
    },
    async editProgram(editingProgram) {
      const res = await fetch(`${this.url}/${this.currentProgram.id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify({
          name: editingProgram.name,
          description: editingProgram.description,
          price: editingProgram.price,
          imgSrc: editingProgram.imgSrc,
        }),
      });
      const data = await res.json();
      this.Program = this.Program.map((m) =>
        m.id === data.id
          ? {
              ...m,
              name: data.name,
              description: data.description,
              price: data.price,
              imgSrc: data.imgSrc,
            }
          : m
      );
      this.currentProgram.name = editingProgram.name;
      this.currentProgram.description = editingProgram.description;
      this.currentProgram.price = editingProgram.price;
      this.currentProgram.imgSrc = editingProgram.imgSrc;
    },
  },
  computed: {
    searchProgram() {
      this.changeNotFound(false);
      if (this.inputSearch == "") {
        return this.Program.slice().reverse();
      } else {
        let result = this.Program.filter((n) =>
          n.name.toLowerCase().includes(this.inputSearch.toLowerCase())
        );
        if (result == "") {
          this.changeNotFound(true);
          return;
        }
        return result;
      }
    },
  },
  async created() {
    this.Program = await this.fetchProgram();
    this.currentProgram = await this.Program[0];
  },
};
</script>