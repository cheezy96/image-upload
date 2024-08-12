<template>
  <div>
    <h2>Upload an Image</h2>
    <input type="file" @change="handleFileUpload" accept="image/*" />
    <button @click="uploadImage">Upload Image</button>
    <div v-if="imageSrc">
      <h3>Preview:</h3>
      <img :src="imageSrc" alt="Image Preview" style="max-width: 100%; height: auto;" />
    </div>
    <p v-if="uploadStatus">{{ uploadStatus }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedFile: null,
      imageSrc: null,
      uploadStatus: '',
    };
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file && file.type.startsWith('image/')) {
        this.selectedFile = file;
        const reader = new FileReader();
        reader.onload = (e) => {
          this.imageSrc = e.target.result;
        };
        reader.readAsDataURL(file);
      } else {
        alert('Please select a valid image file.');
      }
    },
    async uploadImage() {
      if (!this.selectedFile) {
        alert('No file selected.');
        return;
      }

      const formData = new FormData();
      formData.append('image', this.selectedFile);

      try {
        const response = await fetch('http://localhost:3000/upload', {
          method: 'POST',
          body: formData,
        });

        if (!response.ok) {
          throw new Error('Network response was not ok');
        }

        const result = await response.json();
        this.uploadStatus = `Image uploaded successfully: ${result.filePath}`;
      } catch (error) {
        this.uploadStatus = `Upload failed: ${error.message}`;
      }
    },
  },
};
</script>

<style scoped>
/* Add any styles you want here */
</style>
