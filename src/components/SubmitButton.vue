<template>
  <button @click="clickButton" :style="{ 'background-color': '#242331' }"
          class="p-3 mt-8 text-white rounded-md">
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
