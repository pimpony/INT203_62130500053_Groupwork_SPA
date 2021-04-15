<template>
<div class="w-screen " :class="[fixedNav]">
    <div class="container mx-auto flex items-center py-1 justify-between">
        <h1 class="text-2xl text-gray-600 font-medium">
            <router-link to="/">Chloe Ting</router-link>
        </h1>
        <div>
            <router-link to="/about"  >
            <base-button  buttonLabel="About Chloe Ting"  textColor="text-gray-600" borderColor="border-transparent"/> 
            </router-link>
            <base-button buttonLabel="+ Add New Program"  textColor="text-gray-600" borderColor="border-transparent" v-if="$route.path==='/Program'" @click="changeAddItemClicked"/>
            <router-link to="/Program" v-else>
               <base-button buttonLabel="Program"  textColor="text-gray-600" borderColor="border-transparent"/> 
            </router-link>
            
        </div>
    </div>
</div>
</template>
<script>
export default {
    props: ['fixedNav',],
    emits: ['add-clicked'],
    data() {
        return {
            addItemClicked: false
        }
    },
    methods: {
        changeAddItemClicked() {
            this.addItemClicked = true;
            // console.log("nav: "+this.addItemClicked);
            this.$emit('add-clicked',this.addItemClicked);
            this.addItemClicked = false;
        }
    } 
}
</script>