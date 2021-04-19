<template>
  <div
    class="grid grid-cols-4 items-center bg-white rounded-md border hover:bg-gray-200  border-gray-200 mt-4 hover:border-gray-600  "
  >
    <div>
      <img :src="ProgramImg" class="object-cover w-20 h-20 ml-2 my-2" />
    </div>
    <div class="col-span-2">
      <h4 class="font-bold">{{ ProgramName }}</h4>
      <p>{{ ProgramPrice + ".-" }}</p>
    </div>
    <div class="ml-auto">
      <button class="bg-pink-300 py-2 px-3" @click="editClick">
        <i class="fas fa-pen"></i>
      </button>
      <button class="bg-purple-300 py-2 px-3 mx-2" @click="deleteClick">
        <i class="fas fa-trash"></i>
      </button>
    </div>
  </div>
</template>
<script>
export default {
  emits: ["delete-click",'edit-click'],
  props: ["ProgramImg", "ProgramName", "ProgramPrice"],
  methods: {
    deleteClick() {
      this.$emit("delete-click", true);
    },
    editClick() {
      this.$emit("edit-click", true);
    }
  },
};
</script>