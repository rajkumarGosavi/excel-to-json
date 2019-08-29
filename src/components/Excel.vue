<template>
  <div>
    <form>
      <vue-dropzone
        ref="myVueDropzone"
        id="dropzone"
        :options="dropOptions"
        @vdropzone-success="filesUploaded"
      ></vue-dropzone>
      <!-- <button type="button" @vdropzone-file-added.prevent="processFile">Upload</button> -->
    </form>
  </div>
</template>

<script>
import vue2Dropzone from "vue2-dropzone";
import XLSX from "xlsx";
import "vue2-dropzone/dist/vue2Dropzone.min.css";

export default {
  name: "Excel",
  components: {
    vueDropzone: vue2Dropzone
  },
  data() {
    return {
      mFile: null,
      xl: null,
      dropOptions: {
        url: "https://httpbin.org/post",
        maxFilesize: 2,
        acceptedFiles: "application/*",
        thumbnailWidth: 150,
        thumbnailHeight: 150,
        addRemoveLinks: true,
        headers: { "My-Awesome-Header": "header value" }
      }
    };
  },
  methods: {
    filesUploaded(file) {
      this.mFile = file.xhr.response;
      this.mFile = JSON.parse(this.mFile);
      this.mFile = this.mFile.files.file;
      let wb = XLSX.read(this.mFile,{type:"base64"})
      console.log(wb)
    },
  }
};
</script>

<style scoped>
</style>