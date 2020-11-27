<template>
  <div id="home">
    <text-reader />
  </div>
</template>

<script>
// @ is an alias to /src
import TextReader from "../components/TextReader.vue";

export default {
  components: { TextReader },
  name: "Home",
};
</script>

<style scoped>
#home {
  margin: 0 auto;
}
</style>
