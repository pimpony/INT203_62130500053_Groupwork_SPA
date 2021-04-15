<template>
    <button class="ml-4 py-2 px-4 font-medium rounded-md transition ease-in duration-100 border hover:bg-gray-300 hover:text-black hover:border hover:border-white-600" :class="[buttonColor, textColor, borderColor]" :type="buttonType"> {{ buttonLabel }}</button>
</template>
<script>
export default {
    props: ['buttonLabel','buttonColor','textColor','borderColor','buttonType'],
}
</script>