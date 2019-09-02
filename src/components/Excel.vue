<template>
  <div>
    <form>
      <vue-dropzone
        @vdropzone-file-added="fileAdded"
        ref="myVueDropzone"
        id="dropzone"
        :options="dropOptions"
      ></vue-dropzone>
      <button type="button" @click="processFile">Upload</button>
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
      mObjs: [],
      dropOptions: {
        url: "https://httpbin.org/post",
        maxFilesize: 2,
        acceptedFiles: "application/*",
        thumbnailWidth: 150,
        thumbnailHeight: 150,
        addRemoveLinks: true
      }
    };
  },
  methods: {
    // When a file is dragged this event will be called
    // the file will be stored in a vue model variable mFile
    fileAdded(file) {
      this.mFile = file;
    },

    //This is a utility method that will help us remove white spaces,
    // convert string to array and integer values
    strToArr(str) {
      str.trim();
      str = str.replace(/\[|\]/gi, "");
      let arr = str.split(",");
      arr = arr.map(elem => parseInt(elem));
      return arr;
    },

    // This method will process the file and create an array of json objects which can be used to 
    // send to the database or to some other service
    processFile() {
      console.log("Processing file");
      // We have a File object thus, we need a FileReader to read the file
      var reader = new FileReader();
      let self = this;
      let i = 1;
      //OnLoad will be called as soon as the file is loaded see https://developer.mozilla.org/en-US/docs/Web/API/FileReader

      reader.onload = function(e) {
        //store the file's binary string in data
        var data = e.target.result;
        
        // create a workbook see https://www.w3schools.com/jsref/met_win_btoa.asp
        var workbook = XLSX.read(btoa(data), { type: "base64" });

        // select a sheet from a workbook (a workbook contains multiple sheets)
        var sheet = workbook.Sheets[workbook.SheetNames[0]];
        // convert the rows having specified headers of the sheet into json format 
        // (the data is not formatted i.e everything is a string even integers and arrays) 
        var temp = XLSX.utils.sheet_to_json(sheet, {
          header: [
            "emp_id",
            "name",
            "appraiserId",
            "programCordId",
            "progMgrId",
            "officialEmail",
            "designation",
            "quarter1.coursesAllocation",
            "quarter2.coursesAllocation",
            "programId"
          ]
        });

        // iterate over the temp data and format it according to the need
        for (i = 1; i < temp.length; i++) {
          if (temp[i] !== undefined) {
            self.mObjs.push({
              name: temp[i]["name"],
              emp_id: parseInt(temp[i]["emp_id"]),

              appraiserId: parseInt(temp[i]["appraiserId"]),
              programCordId: parseInt(temp[i]["programCordId"]),
              programMgrId: parseInt(temp[i]["progMgrId"]),
              programId: self.strToArr(temp[i]["programId"]),

              officialEmail: temp[i]["officialEmail"],
              designation: temp[i]["designation"],

              isDeleted: false,

              quarter1: {
                coursesAllocation: self.strToArr(
                  temp[i]["quarter1.coursesAllocation"]
                )
              },
              quarter2: {
                coursesAllocation: self.strToArr(
                  temp[i]["quarter2.coursesAllocation"]
                )
              }
            });
          }
        }

        // if all the rows are formatted and stored in mObjs then do some work
        if (i === temp.length) {
          // self.InsertEmployee();
          console.log(self.mObjs);
        }
      };
      // Loads the file onto the reader and read it as a binary string
      reader.readAsBinaryString(this.mFile);
    }
  }
};
</script>

<style scoped>
</style>