<template>
  <v-app>
    <v-main>
      <EquipmentCost />
    </v-main>
  </v-app>
</template>

<script>
import EquipmentCost from "./components/EquipmentCost";

export default {
  name: "App",

  components: {
    EquipmentCost,
  },

  data: () => ({
    //
  }),
};
</script>
