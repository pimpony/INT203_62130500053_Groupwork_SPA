<template>
  <div class="min-h-screen">
    <video autoplay muted loop id="myVideo" class="fixed right-0 bottom-0 min-w-full min-h-full -z-99">
      <source src="../assets/Challenge_1080p.mp4" type="video/mp4" />
    </video>

    <div class="h-screen w-screen text-center flex justify-center items-center text-gray-600">
      <div>
        <h1 class="text-3xl font-bold">Chloe Ting Free Program</h1>
        <h2 class="text-xl mt-10"> Website for Free Program Workout</h2>
        <br />
        <router-link to="/Program">
          <base-button
            buttonLabel="Program"
            buttonColor="bg-gray-400"
            textColor="text-white"
            borderColor="border-transparent"
            class="text-xl"
          />
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  components: {},
};
</script>
