<template>
  <button class="btn btn-info" @click="onPickFile">Upload Syllabus</button>
  <input
    type="file"
    style="display: none"
    ref="fileInput"
    accept="application/pdf"
    @change="onFilePicked"/>
    <h1>Data</h1>
    <table class="table">
    <thead>
      <tr>
        <th scope="col">Type</th>
        <th scope="col">Description</th>
        <th scope="col">Due Date</th>
        <th scope="col">Weight</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in data" :key="item.description">
        <td>{{item.type}}</td>
        <td>{{item.description}}</td>
        <td>{{item.deadline === null ? item.on_going ? 'On-going' : 'TBD' : item.deadline}}</td>
        <td>{{item.weight}}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
const axios = require("axios");
export default {
  name: 'Example',
  data () {
    return {
      file: null,
      data: null
    }
  },
  methods: {
    onPickFile () {
      this.$refs.fileInput.click()
    },
    onFilePicked (event) {
      const files = event.target.files
      const filename = files[0].name
      const fileReader = new FileReader()
      fileReader.addEventListener('load', () => {
        this.fileUrl = fileReader.result
      })
      fileReader.readAsDataURL(files[0])
      this.file = files[0]
      if (confirm(`Do you want to use this syllabus: ${filename}?`)) {
        let formData = new FormData();
        formData.append('syllabus', this.file);
        axios.post( `${process.env.VUE_APP_API_URL}/manager/parser`,
          formData,
            {
            headers: {
                'Content-Type': 'multipart/form-data'
            }
          }
        ).then((res) => {
          this.data = res.data;
        })
        .catch((err) => {
          this.file = null
          console.log(err.message);
        });
      } else {
        this.file = null
      }
    }
  }
}
</script>