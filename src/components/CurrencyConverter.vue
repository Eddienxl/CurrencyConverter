<template>
    <div class="container-fluid bg-gradient vh-100 d-flex align-items-center justify-content-center">
      <div class="row justify-content-center w-100">
        <div class="col-md-6">
          <div class="card p-4 shadow-lg rounded-4 bg-light">
            <h2 class="text-center fw-bold">Konversi Mata Uang</h2>
            <p class="text-center">
              By 
              <a href="https://www.vatcomply.com/documentation" target="_blank">VATComply</a>
            </p>
  
            <div class="mb-3">
              <label class="form-label fw-bold">Dari:</label>
              <select v-model="fromCurrency" class="form-select">
                <option value="" disabled selected>Pilih Mata Uang</option>
                <option v-for="(name, code) in currencies" :key="code" :value="code">
                  {{ code }} - {{ name }}
                </option>
              </select>
            </div>
  
            <div class="mb-3">
              <label class="form-label fw-bold">Jumlah:</label>
              <input 
                type="number" 
                v-model="amount" 
                class="form-control"
                placeholder="Masukkan jumlah"
              />
            </div>
  
            <div class="mb-3">
              <label class="form-label fw-bold">Ke:</label>
              <select v-model="toCurrency" class="form-select">
                <option value="" disabled selected>Pilih Mata Uang</option>
                <option v-for="(name, code) in currencies" :key="code" :value="code">
                  {{ code }} - {{ name }}
                </option>
              </select>
            </div>
  
            <button 
              @click="convertCurrency" 
              class="btn btn-primary w-100 fs-5 py-2"
              :disabled="!fromCurrency || !toCurrency || !amount"
            >
              Konversi
            </button>
          </div>
  
          <div v-if="convertedAmount !== null" class="mt-4">
            <div class="card p-4 shadow rounded-4 bg-white text-center">
              <h3 class="fw-bold text-primary">Hasil Konversi</h3>
              <p><strong>Base:</strong> {{ base || "USD" }}</p>
              <p><strong>Tanggal:</strong> {{ date || "Belum dikonversi" }}</p>
              <p class="fs-2 fw-bold text-success">
                {{ amount }} {{ fromCurrency }} = {{ convertedAmount }} {{ toCurrency }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <style>
  .bg-gradient {
    background: linear-gradient(135deg, #4facfe, #00f2fe);
  }
  </style>
  
  <script>
  export default {
    data() {
      return {
        currencies: {},
        fromCurrency: "", // Kosong di awal
        toCurrency: "", // Kosong di awal
        amount: null, // Kosong di awal
        convertedAmount: null,
        base: "USD", // Menambahkan nilai default
        date: "",
      };
    },
    async mounted() {
      try {
        const response = await fetch("https://api.vatcomply.com/currencies");
        const data = await response.json();
        this.currencies = data;
      } catch (error) {
        console.error("Gagal mengambil daftar mata uang:", error);
      }
    },
    methods: {
      async convertCurrency() {
        if (!this.fromCurrency || !this.toCurrency || !this.amount) {
          alert("Harap isi semua field terlebih dahulu!");
          return;
        }
  
        try {
          const response = await fetch(
            `https://api.vatcomply.com/rates?base=${this.fromCurrency}`
          );
          const data = await response.json();
          this.base = data.base;
          this.date = data.date;
          this.convertedAmount = (this.amount * data.rates[this.toCurrency]).toFixed(2);
        } catch (error) {
          console.error("Gagal mengonversi mata uang:", error);
        }
      }
    }
  };
  </script>
  