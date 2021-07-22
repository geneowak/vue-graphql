<template>
  <div class="rockets">
    <RocketList />
  </div>
</template>
<script>
// @ is an alias to /src
import RocketList from "@/components/RocketList.vue";

export default {
  name: "Home",
  components: {
    RocketList,
  },
};
</script>
