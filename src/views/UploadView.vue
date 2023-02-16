<template>
  <div>
    <div class="container">
      <div class="large-12 medium-12 small-12 cell">
        <form @submit.prevent="onSubmit" enctype="multipart/form-data">
          <input
            type="text"
            class="form-control mb-2 mt-2"
            style="width: auto"
            v-model="nama"
          />
          <input
            type="file"
            ref="file"
            @change="onSelect"
          />
          <button>Submit</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { API_URL } from "../components/Url";

export default {
  name: "UploadView",
  data() {
    return {
      nama: "",
      file: "",
    };
  },
  methods: {
    onSelect(){
        const file = this.$refs.file.files[0];
        this.file = file;
    },
    onSubmit(){
        const formData = new FormData();
        formData.append('nama',this.nama);
        formData.append('file',this.file);
        axios
        .post(API_URL+"unggah",formData)
        .then((response)=>{
            console.log(response.data);
        })
    }
  },
};
</script>

<style></style>
