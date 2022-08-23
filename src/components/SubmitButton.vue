<template>
  <button @click="clickButton" :style="{ 'background-color': '#242331', 'color': 'white' }"
          class="p-3 mt-8 text-black rounded-md">
    Enregistrer
  </button>
</template>

<script>
export default {
  emits: ['submit'],
  props: {
    color: {
      type: String,
    },
  },
  methods: {
    clickButton() {
      this.$emit("submit");
    },
  },
};
</script>
