<template>
  <v-app>
    <v-main>
      <v-container>
        <h1>Welcome to Simplify App</h1>
        <v-file-input
          label="Upload a PDF"
          accept=".pdf"
          show-size
          @change="handleFileUpload"
        ></v-file-input>

        <v-card class="mt-4">
  <v-card-title>Simplified Text</v-card-title>
  <v-card-text>
    <div v-if="simplifiedText">
      <p>{{ simplifiedText }}</p>
    </div>
    <div v-else>
      <p>No text simplified yet. Upload a PDF to get started!</p>
    </div>
  </v-card-text>
</v-card>

<v-progress-circular
  indeterminate
  color="primary"
  v-if="loading"
  class="mt-4"
></v-progress-circular>

      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      selectedFile: null,
      simplifiedText: null,
      loading: false,
    };
  },
  methods: {
    handleFileUpload(file) {
      this.selectedFile = file;
      this.loading = true; //simulate loading state
      setTimeout(() => {
        this.loading = false; //reset after testing
        this.simplifiedText = "sample simplified text."; //placeholder
      }, 2000);
      console.log("File uploaded:", file);
    },
  },
};
</script>

