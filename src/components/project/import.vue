<!--this file is not part of the original odk central project
created by hafsa
-->
<template>
    <modal id="modal">
      
      <template #title>Import Projects</template>
      <template #body> 
      <div class="modal-introduction">
          <p>(Introduction)</p>
        </div>
          
          <form >
            <form-group ref="file" type="file" v-model="file"
            :placeholder="'no file imported'" 
            @change="handleFileUpload" accept=".xls,.xlsx" required autocomplete="off"/>
            <div class="modal-actions">
              <button type="submit" class="btn btn-primary" @click="showSheet">Show</button>
              <button class="btn btn-link" @click="closeModal">Cancel</button>
            </div>
          </form>
        </template> 
    </modal>
</template>
  
  <script>
  import * as XLSX from 'xlsx';
  import FormGroup from '../form-group.vue';
  import Modal from '../modal.vue';
  export default {
    name:"FileUploadModal",
    data() {
      return {
        selectedFile: null,
      };
    },
    components : { 
      FormGroup, Modal
    },
    methods: {
      handleFileUpload(event) {
        console.log("File uploaded", event.target.files[0]);
        this.selectedFile = event.target.files[0];
      },
      showSheet() {
        console.log("Show button clicked");
        if (!this.selectedFile) {
          alert('Please select a file.');
          return;
        }
  
        this.readExcelData(this.selectedFile).then((sheetData) => {
          console.log("Excel data read", sheetData);
          this.$emit('file-selected', sheetData); // Emit event to parent component (file1.vue)
        });
      },
      readExcelData(file) {
        return new Promise((resolve, reject) => {
          const reader = new FileReader();
          reader.onload = (e) => {
            const data = e.target.result;
            const workbook = XLSX.read(data, { type: 'binary' });
            const sheetName = workbook.SheetNames[0];
            const sheet = workbook.Sheets[sheetName];
            const sheetData = XLSX.utils.sheet_to_json(sheet, { header: 1 });
            resolve(sheetData);
          };
          reader.onerror = (error) => {
            console.error("Error reading file", error);
            reject(error);
          };
          reader.readAsBinaryString(file);
        });
      },
      closeModal() {
        console.log("Cancel button clicked");
        this.$emit('close'); // Emit close event to parent component (file1.vue)
      },
    },
  };
  </script>
  
  <style scoped>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  

  </style>
  