<template>
  <div class>
    <PlayAreaCanvas/>
  </div>
</template>

<script>
// @ is an alias to /src
import PlayAreaCanvas from "@/components/PlayAreaCanvas.vue";

export default {
  name: "home",
  components: {
    PlayAreaCanvas
  }
};
</script>
