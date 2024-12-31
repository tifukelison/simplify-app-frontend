<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" md="8">
        <v-card>
          <v-card-title>Upload PDF</v-card-title>
          <v-card-text>
            <v-file-input
              label="Select File"
              accept=".pdf, .docx"
              outlined
              dense
              @change="handleFileUpload"
            ></v-file-input>
            <v-btn @click="submitFile" color="primary" :disabled="!file">
              Upload
            </v-btn>
          </v-card-text>
          <v-card-text v-if="pdfText">
  <h2>Extracted Text:</h2>
  <transition-group name="fade-slide" tag="div">
    <p v-for="(paragraph, index) in visibleText" :key="index" class="paragraph">
      {{ paragraph }}
    </p>
  </transition-group>
    <v-btn v-if="showExpandButton" @click="expandText" color="primary" class="mt-2">
      Expand More
    </v-btn>
</v-card-text>



        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      pdfText: '',
      file: null,
      formattedText: [],
      visibleText: [],
      currentChunk: 0,
      chunkSize: 5, // Number of paragraphs to display initially
    };
  },
  computed: {
    showExpandButton() {
      return this.currentChunk * this.chunkSize < this.formattedText.length;
    },
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target?.files?.[0];
      if (file) {
        this.file = file;
        console.log("Selected file:", this.file);
      } else {
        console.error("No file selected or invalid event:", event);
      }
    },
    async submitFile() {
      if (!this.file) {
        alert("Please select a file first.");
        return;
      }

      const formData = new FormData();
      formData.append("file", this.file);

      try {
        const response = await fetch("http://localhost:5000/upload", {
          method: "POST",
          body: formData,
        });

        if (!response.ok) {
          throw new Error("Network response was not ok");
        }

        const data = await response.json();
        this.pdfText = data.text;
        this.formattedText = data.text.split("\n").filter((line) => line.trim() !== "");
        this.initializeVisibleText();
      } catch (error) {
        console.error("Error uploading file:", error);
        alert("Error uploading file.");
      }
    },
    initializeVisibleText() {
      this.currentChunk = 1;
      this.visibleText = this.formattedText.slice(0, this.chunkSize);
    },
    expandText() {
      this.currentChunk++;
      this.visibleText = this.formattedText.slice(0, this.currentChunk * this.chunkSize);
    },
  },
};

</script>

<style>
.v-container {
  margin-top: 60px;
}

.paragraph {
  margin-bottom: 1.5em; /* Adds space between paragraphs */
  line-height: 1.6; /* Improves line spacing for readability */
}

h2 {
  color: #3f51b5; /* Stylish color for headers */
  font-size: 1.8em; /* Makes headers stand out */
  margin-bottom: 0.5em;
}

p {
  font-size: 1.1em; /* Slightly larger font for better reading */
  color: #333; /* Soft black for less strain on the eyes */
}

.highlight {
  font-weight: bold;
  color: #d32f2f; /* Highlighted text in red */
}

.v-btn {
  display: block;
  margin: auto;
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 0.5s, transform 0.5s;
}

.fade-slide-enter-from {
  opacity: 0;
  transform: translateY(-20px);
}

.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

</style>
