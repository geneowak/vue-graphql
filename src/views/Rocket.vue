<template>
  <div class="rockets">
    <RocketItem :rocketId="id" />
  </div>
</template>
<script>
// @ is an alias to /src
import RocketItem from "@/components/RocketItem.vue";

export default {
  name: "Home",
  props: { id: { required: true } },
  components: {
    RocketItem,
  },
};
</script>
