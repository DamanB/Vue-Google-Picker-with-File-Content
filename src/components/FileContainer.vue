<template>
  <div id="FileContent">
    <h2>{{ fileName }}</h2>
    <div class="file-content">
      {{ fileContent }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    fileName: {
        type: String,
        required: true,
    },
    fileContent: {
        type: String,
        required: true,
    },
  }
};
</script>

<style>
</style>