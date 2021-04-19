<template>
  <div class="flex justify-center items-center bg-cover  bg-center w-screen min-h-screen  " style="background-image: url(https://www.chloeting.com/splash/bg-img.jpeg)">
    <div class=" w-screen flex justify-center items-center ">
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
            We can't find the Program
          </p>
          <!-- Select Programs show   -->
       <div class=" bg-gray-300 bg-opacity-75 rounded-xl "  >
        <!-- <span @click="closeProgram"  class="text-xl bg-pink close-icon text-black flex justify-end mr-5" >
          x
          </span> -->
          <div class="col-span-2 my-3">
            <div class="grid grid-cols-2 ml-8  ">
              <div>             
                <h1 class="text-xl font-bold mt-4">Program name : {{ programnow.name }}</h1>
                <p class="mt-2">Type :<br/> {{ programnow.type }}</p>
                <p class="mt-2">Equipment :<br/> {{ programnow.equipment }}</p>
              </div>
              <div class="flex justify-center items-center">
                  <div class=" grid grid-cols ">
                      <h1 class="bg-gray-400 text-white text-2xl flex justify-center rounded-full items-center my-2 ">
                        {{ programnow.price + ".-" }}
                      </h1>
                      <img
                        :src="programnow.imgSrc"
                        class="w-32 h-44 flex justify-center items-center mb-2 "
                      />
                  </div>
              </div>
          </div>
        </div> 
      </div>
          

          <!-- Show Programs all  -->
          <div class=" ">
            <div v-for="p in searchProgram" :key="p.id">
              <Program-block
                :ProgramImg="p.imgSrc"
                :ProgramName="p.name"
                :ProgramPrice="p.price"
                :ProgramEquipment="p.equipment"
                @click="selectedProgram(p)"
                @delete-click="deleteProgram(p.id)"
                @edit-click="openEditModal"
              />
            </div>
          </div>
         
        </div>
      </div>
        
     

    </div>
  </div>

  <modal v-if="addClicked"  @close="changeAddItemClicked" formLabel="Add New Program" @save-Program="addNewProgram" ></modal>
  <modal v-if="editClicked" @close="changeEditItemClicked" formLabel="Edit Program" :ProgramImgFromDb="programnow.imgSrc" :name="programnow.name"
    :type="programnow.type"  :equipment="programnow.equipment" :price="programnow.price" @save-Program="editProgram"></modal>
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
      programnow: [],
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
      this.programnow = false;
      
    },
    selectedProgram(Program) {
      this.programnow = Program;
    },
    async addNewProgram(newProgram) {
      const res = await fetch(this.url, {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify({
          name: newProgram.name,
          type: newProgram.type,
          price: newProgram.price,
          equipment: newProgram.equipment,
          imgSrc: newProgram.imgSrc,
        }),
      });
      const data = await res.json();
      this.Program = [...this.Program, data];
      this.programnow = this.Program[this.Program.length - 1];
    },
    async deleteProgram(id) {
      const res = await fetch(`${this.url}/${id}`, {
        method: "DELETE",
      });
      res.status === 200
        ? (this.Program = this.Program.filter((m) => m.id !== id))
        : alert("Error to delete Program");
      this.programnow = this.Program[this.Program.length - 1];
    },
    openEditModal(value) {
      this.editClicked = value;
    },
    async editProgram(editingProgram) {
      const res = await fetch(`${this.url}/${this.programnow.id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify({
          name: editingProgram.name,
          type: editingProgram.type,
          price: editingProgram.price,
          equipment: editingProgram.equipment,
          imgSrc: editingProgram.imgSrc,
        }),
      });
      const data = await res.json();
      this.Program = this.Program.map((p) =>
        p.id === data.id
          ? {
              ...p,
              name: data.name,
              type: data.type,
              price: data.price,
              equipment: data.equipment,
              imgSrc: data.imgSrc,
            }
          : p
      );
      this.programnow.name = editingProgram.name;
      this.programnow.type = editingProgram.type;
      this.programnow.equipment = editingProgram.equipment;
      this.programnow.price = editingProgram.price;
      this.programnow.imgSrc = editingProgram.imgSrc;
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
    this.programnow = await this.Program[0];
  },
};
</script>