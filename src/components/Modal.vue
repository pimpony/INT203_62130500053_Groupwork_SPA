<template>
  <div class="fixed z-10 inset-0 overflow-y-auto"  >
    <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" ></div>
      <span class="hidden sm:inline-block sm:align-middle sm:h-screen"  >&#8203;</span >
      <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-2xl sm:w-full">
        <form @submit.prevent="checkForm">
          <div class="bg-gray-200 px-4 pt-5 pb-4">
            <div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
              <h3 class="text-2xl font-medium text-gray-900" id="modal-title">
                {{ formLabel }}
              </h3>
              <div class="mt-4">
                <div class="grid grid-cols-3 gap-6">
                  <div>
                    <img
                      :src="ProgramImage"
                      class="object-cover w-48 h-48 "
                    />
                    <br />
                    <div class="relative overflow-hidden inline-block">
                      <base-button
                        buttonLabel="Upload Image"
                        buttonColor="bg-grey-300"
                        textColor="text-black"
                        borderColor="border-black"
                      >
                      </base-button>
                      <input
                        id="file-input"
                        type="file"
                        @change="uploadPhoto"
                        class="absolute top-0 left-0 opacity-0 h-10"
                      />
                    </div>
                  </div>
                  <div class="col-span-2">
                    <div>
                      <label for="ProgramName" class="text-lg font-medium"
                        >Program Name</label
                      >
                      <br />
                      <input
                        type="text"
                        id="ProgramName"
                        v-model="ProgramName"
                        name="ProgramName"
                        class="border w-11/12 mt-2 rounded h-8 text-md"
                      />
                      <br>
                      <i class="text-sm text-red-500" v-if="errors">
                        {{ errors.ProgramName }}</i
                      >
                    </div>
                    <div class="mt-4">
                      <label for="ProgramDescript" class="text-lg font-medium"
                        >Program Description
                        </label
                      >
                      <br />
                      <textarea
                        rows="5"
                        cols="50"
                        id="ProgramDescript"
                        v-model="ProgramDescript"
                        name="ProgramDescript"
                        class="border w-11/12 mt-2 rounded text-mg"
                      />
                    </div>
                    <div class="mt-2">
                      <label for="ProgramPrice" class="text-lg font-medium"
                        >Price</label
                      >
                      <br />
                      <input
                        type="number"
                        id="ProgramPrice"
                        v-model="ProgramPrice"
                        name="ProgramPrice"
                        class="border w-1/5 mt-2 rounded h-8 text-md"
                      />
                      <br>
                      <i class="text-sm text-red-500" v-if="errors">
                        {{ errors.ProgramPrice }}</i
                      >
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="bg-gray-200 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
            <base-button
              buttonLabel="Save"
              buttonColor="bg-pink-300"
              textColor="text-white"
              buttonType="submit"
            />
            <base-button
              buttonLabel="Cancel"
              buttonColor="bg-purple-300"
              textColor="text-black"
              borderColor="border-grey-400"
              @click="closeCurrentModal"
            />
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import ProgramImage from "../assets/placeholder.png";

const constraints = {
  ProgramName: {
    presence: true,
  },
  ProgramPrice: {
    presence: true,
    numericality: {
      greaterThanOrEqualTo: 0,
    },
  },
};

export default {
  props: ["formLabel", "ProgramImgFromDb", "name", "description", "price"],
  emits: ["close", "save-Program"],
  data() {
    return {
      ProgramImage: this.ProgramImgFromDb ? this.ProgramImgFromDb : ProgramImage,
      ProgramName: this.name,
      ProgramDescript: this.description,
      ProgramPrice: this.price,
      errors: null,
    };
  },
  methods: {
    closeCurrentModal() {
      this.$emit("close", true);
    },
    uploadPhoto(e) {
      const file = e.target.files[0] || e.dataTransfer.files[0];
      const reader = new FileReader();
      reader.onload = (e) => {
        this.ProgramImage = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    checkForm() {
      console.log("he");
      var validate = require("validate.js");
      this.errors = validate(
        {
          ProgramName: this.ProgramName,
          ProgramDescript: this.ProgramDescript,
          ProgramPrice: this.ProgramPrice,
        },
        constraints
      );
      if (this.errors) {
        console.log(this.errors);
      } else {
        this.saveProgramInfo();
        this.closeCurrentModal();
      }
    },
    saveProgramInfo() {
      let Program = {
        name: this.ProgramName,
        description: this.ProgramDescript,
        price: this.ProgramPrice,
        imgSrc: this.ProgramImage
      };
      this.$emit("save-Program", Program);
    },
  },
};
</script>