<template>
  <SceneEmpty1></SceneEmpty1>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import SceneEmpty1 from "@/components/SceneEmpty1.vue";

@Options({
  components: { SceneEmpty1 }
})
export default class Home extends Vue {}
</script>
