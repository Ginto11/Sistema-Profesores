<template>
    <ComponentePrincipal></ComponentePrincipal>
</template>

<script lang="js">
  import ComponentePrincipal from './components/ComponentePrincipal.vue';

  import { defineComponent } from 'vue';

  export default defineComponent({
    name: "App",
    components: {
      ComponentePrincipal
    }
  })
</script>

<style>
  body{
        background-color: #292F36;
        color: #DADAD9;
    }
</style>