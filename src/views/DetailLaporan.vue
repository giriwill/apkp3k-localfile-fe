<template>
  <div>
    <NavBar />
    <div class="container">
      <!-- membuat breaccrumb  -->
      <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
          <li class="breadcrumb-item">
            <router-link to="/">Home</router-link>
          </li>
          <li class="breadcrumb-item">
            <router-link to="/laporan">Laporan</router-link>
          </li>
          <li class="breadcrumb-item active" aria-current="page">
            <strong>Detail Laporan</strong>
          </li>
        </ol>
      </nav>
      <!-- membuat card identitas guru  -->
      <div class="card" style="width: 25rem">
        <div class="card-header"><strong>Identitas ASN</strong></div>
        <ul class="list-group list-group-flush">
          <li class="list-group-item">
            {{ this.guruku.nama }}<br />
            NIP. {{ this.guruku.nip }}<br />
          </li>
        </ul>
      </div>
      <hr />
      <!-- membuat card inputan kegiatan  -->
      <div class="card" style="width: auto">
        <div class="card-header"><strong>Input Kegiatan Disini</strong></div>
        <ul class="list-group list-group-flush">
          <li class="list-group-item">
            <!-- dibagi dua kolom  -->
            <form
              class=""
              @submit.prevent="uploadKegiatan"
              enctype="multipart/form-data"
            >
              <div class="row">
                <div class="col-md-6">
                  <!-- isi kolom ke 1 -->
                  <!-- -->
                  <label for="nip">Tanggal</label>
                  <div @click="munculkanLaporan">
                    <b-form-datepicker
                      v-model="tanggal"
                      id="datepicker-dateformat2"
                      placeholder="Klik Tanggal"
                      :date-format-options="{
                        year: 'numeric',
                        month: 'numeric',
                        day: 'numeric',
                      }"
                      locale="en"
                    ></b-form-datepicker>
                  </div>

                  <!--  -->
                  <div class="form-group mt-3">
                    <label for="nip">Hari</label>
                    <select
                      v-model="hari"
                      class="form-control"
                      id="exampleFormControlSelect1"
                    >
                      <option>Senin</option>
                      <option>Selasa</option>
                      <option>Rabu</option>
                      <option>Kamis</option>
                      <option>Jumat</option>
                    </select>
                  </div>
                  <!--  -->
                  <div class="row">
                    <div class="col">
                      <div class="form-group">
                    <label for="nama">Lama Kegiatan (Dalam Menit)</label>
                    <input
                      v-model="durasi"
                      type="number"
                      class="form-control"
                    />
                  </div>
                    </div>                    
                  </div>
                  <div class="form-group">
                    <label for="nama">Deskripsi Kegiatan</label>
                    <input
                      v-model="kegiatan"
                      type="text"
                      class="form-control"
                    />
                  </div>
                  <button class="btn btn-info">
                    <b-icon-upload></b-icon-upload> Upload
                  </button>
                  <!--  -->
                </div>
                <div class="col-md-6">
                  <!-- isi kolom ke 2  -->
                  <div class="form-group">
                    <label for="nama">Upload Foto Kegiatan</label>
                    <input
                      type="file"
                      ref="file"
                      @change="onSelect"
                      class="form-control"
                      placeholder="Wajib link Google Drive"
                    />
                  </div>

                  <i>Ket. Untuk Foto Minimal satu buah yang diisi</i>
                </div>
              </div>
            </form>
          </li>
        </ul>
      </div>
      <hr />
      <div class="card" style="width: auto">
        <div class="card-header">
          <strong v-if="this.tanggal"
            >List Kegiatan Pada Tanggal
            {{
              this.tanggal.split("-")[2] +
              "-" +
              this.tanggal.split("-")[1] +
              "-" +
              this.tanggal.split("-")[0]
            }}
            <button
              @click="munculkanLaporan"
              class="btn btn-success btn-sm float-right"
            >
              <b-icon-save></b-icon-save> RELOAD
            </button>
            <router-link
              target="blank"
              class="btn btn-info btn-sm float-right mr-1"
              :to="'/cetak/' + this.guruku.nip"
              ><b-icon-printer></b-icon-printer> CETAK
            </router-link>
          </strong>
          <strong v-else
            >Silahkan Tentukan Tanggal Untuk Melihat List Kegiatan</strong
          >
        </div>
        <ul class="list-group list-group-flush">
          <li class="list-group-item">
            <!-- tabel list kegiatan disini  -->
            <table id="tabelLaporan" class="table table-hover">
              <thead>
                <tr>
                  <th>No.</th>
                  <th>Hari</th>
                  <th>Tanggal</th>
                  <th>Jumlah Menit</th>
                  <th>Uraian Kegiatan</th>
                  <th width="400">Foto</th>
                  <th width="20">Aksi</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(laporans, index) in laporan" :key="laporans.id">
                  <th>{{ index + 1 }}</th>
                  <td>{{ laporans.hari }}</td>
                  <td>
                    {{ laporans.tanggal }}-{{ laporans.bulan }}-{{
                      laporans.tahun
                    }}
                  </td>
                  <td>{{ laporans.durasi }} Menit</td>
                  <td>{{ laporans.kegiatan }}</td>
                  <td>
                    <span>
                      <img
                        width="120"
                        :src=laporans.url
                      />
                    </span>                    
                  </td>
                  <td>
                    <button
                      class="btn btn-warning btn-sm"
                      @click="hapusLaporan(laporans.id)"
                    >
                      <b-icon-trash></b-icon-trash>
                    </button>
                  </td>
                </tr>
                <tr v-if="this.totalMenit">
                  <td></td>
                  <td></td>
                  <td><strong>Total</strong></td>
                  <td>
                    <strong>{{ this.totalMenit }} Menit</strong>
                  </td>
                  <td></td>
                  <td></td>
                  <td></td>
                </tr>
                <tr v-if="this.totalMenit">
                  <td></td>
                  <td></td>
                  <td>
                    <strong v-if="this.totalMenit < 450" style="color: orange"
                      >Kekurangan</strong
                    >
                    <strong v-if="this.totalMenit > 450" style="color: blue"
                      >Kelebihan</strong
                    >
                  </td>
                  <td>
                    <strong v-if="this.totalMenit < 450"
                      >{{ 450 - this.totalMenit }} Menit</strong
                    >
                    <strong v-if="this.totalMenit > 450"
                      >{{ this.totalMenit - 450 }} Menit</strong
                    >
                  </td>
                  <td></td>
                  <td></td>
                  <td></td>
                </tr>
              </tbody>
            </table>
            <!--  -->
          </li>
        </ul>
      </div>
    </div>
    <FooTer />
  </div>
</template>

<script>
import NavBar from "../components/NavBar.vue";
import FooTer from "../components/FooTer.vue";
import axios from "axios";
import swal from "sweetalert";
import { API_URL } from "../components/Url";

export default {
  name: "DetailLaporan",
  components: {
    NavBar,
    FooTer,
  },
  data() {
    return {
      guruku: [],
      laporan: [],
      tanggal: "",
      hari: "",
      dariJam: "",
      sampaiJam: "",
      durasi: "",
      kegiatan: "",
      file: "",
      totalMenit: "",
    };
  },
  methods: {
    onSelect() {
      const file = this.$refs.file.files[0];
      this.file = file;
    },
    uploadKegiatan() {
      // pastikan tidak ada form yang kosong
      if (
        this.tanggal &&
        this.hari &&
        this.durasi &&
        this.kegiatan &&
        this.file
      ) {
        const formData = new FormData();
        formData.append("nip", this.guruku.nip);
        formData.append("tanggal", this.tanggal.split("-")[2]);
        formData.append("bulan", this.tanggal.split("-")[1]);
        formData.append("tahun", this.tanggal.split("-")[0]);
        formData.append("hari", this.hari);
        formData.append("dari", this.dariJam);
        formData.append("sampai", this.sampaiJam);
        formData.append("durasi", this.durasi);
        formData.append("kegiatan", this.kegiatan);
        formData.append("file", this.file);
        axios.post(API_URL + "laporan", formData)
        .then(() => {
            swal("Terima Kasih", "Kegiatan Berhasil Di Upload", "success", {
              timer: 2000,
              button: false,
            });
            this.dariJam = "";
            this.sampaiJam = "";
            this.kegiatan = "";
            this.file = "";
          })        
      } else {
        swal("Peringatan", "Daftar Isian Belum Lengkap", "warning", {
          timer: 2000,
          button: false,
        });
      }
    },
    munculkanLaporan() {
      //munculkal laporan
      axios
        .post(API_URL + "laporan-khusus", {
          nip: this.guruku.nip,
          tanggal: this.tanggal.split("-")[2],
          bulan: this.tanggal.split("-")[1],
          tahun: this.tanggal.split("-")[0],
        })
        .then((response) => {
          this.laporan = response.data;
          // console.log("list laporan loaded");
        })
        .catch();
      // munculkan jumlah menit
      axios
        .post(API_URL + "laporan-sum", {
          nip: this.guruku.nip,
          tanggal: this.tanggal.split("-")[2],
          bulan: this.tanggal.split("-")[1],
          tahun: this.tanggal.split("-")[0],
        })
        .then((response) => {
          this.totalMenit = response.data;
          // console.log(response);
        })
        .catch();
    },
    hapusLaporan(id) {
      swal({
        title: "Yakin Akan Menghapus Ini ?",
        text: "Kegiatan ini akan terhapus secara permanen",
        icon: "warning",
        buttons: true,
        dangerMode: true,
      }).then((willDelete) => {
        if (willDelete) {
          // skrip delete
          axios
            .delete(API_URL + "laporan/" + id)
            .then(() => {
              //munculkan laporan
              axios
                .post(API_URL + "laporan-khusus", {
                  nip: this.guruku.nip,
                  tanggal: this.tanggal.split("-")[2],
                  bulan: this.tanggal.split("-")[1],
                  tahun: this.tanggal.split("-")[0],
                })
                .then((response) => {
                  this.laporan = response.data;
                  console.log(response.data);
                })
                .catch();
              // munculkan jumlah menit
              axios
                .post(API_URL + "laporan-sum", {
                  nip: this.guruku.nip,
                  tanggal: this.tanggal.split("-")[2],
                  bulan: this.tanggal.split("-")[1],
                  tahun: this.tanggal.split("-")[0],
                })
                .then((response) => {
                  this.totalMenit = response.data;
                  // console.log(response);
                })
                .catch();
            })
            .catch();
          //
          swal("Kegiatan Telah Dihapus", {
            icon: "success",
          });
        } else {
          // swal("Sip, Data Anda Aman !");
        }
      });
    },
  },
  mounted() {
    axios
      .get(API_URL + "guru/" + this.$route.params.id)
      .then((response) => {
        this.guruku = response.data;
        if(this.guruku.jenis == 'tu'){
          this.$router.push("/laporan2/"+this.guruku.id);
        }
      })
      .catch();
  },
};
</script>

<style></style>
