<template>
  <draggable v-model="items">
    <template v-slot:item="{item}">
      <!-- example -->
      <div>
        {{item.title}}
      </div>
      <!-- or your own template -->
    </template>
  </draggable>
</template>

<script>
import Draggable from "vue3-draggable";

export default {
  name: "Dragg",
  components: {
    Draggable,
  },
  data(){
    return{
      items:[
        {title:'Aaa'},
        {title:'Bbb'},
        {title:'Ccc'}
      ]
    }
  }
}
</script>

<style scoped>

</style>