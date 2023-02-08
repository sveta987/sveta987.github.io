<template>
<div class="bg-white rounded-md p-[15px] shadow-2xl w-[60%]">
<div class="flex justify-center">
  <img src="../assets/images/error.png" alt="error-icon" class="w-[50px] h-[50px]">
</div>
  <h5 class="text-center text-3xl my-[15px]">Oh snap!</h5>
  <slot></slot>
  <div class="flex justify-center">
    <button class="text-gray-900 bg-white border border-gray-300 focus:outline-none hover:bg-gray-100 focus:ring-4 focus:ring-gray-200 font-medium rounded-lg text-sm px-10 py-1.5 mr-2 mt-2"
            @click="$emit('closeErrorMessage')">
      OK
    </button>
  </div>
</div>
</template>

<script>
export default {
  name: "ErrorMessage",
  emits:['closeErrorMessage'],
}
</script>
