<script setup>
import { ref, computed } from 'vue'
import axios from 'axios'

const api_url = "https://ed866f76-a1be-4454-a423-9d8d88bba8eb.mock.pstmn.io"

const products = ref([])

const product = ref({
  id: "",
  name: "",
  category: "",
  price: 0,
  quantity: 0,
})

const search_value = ref("")
const filteredProducts = computed(() => {
  return products.value.filter(p => {
    return p.name.toLowerCase().includes(search_value.value.toLowerCase())
      || p.category.toLowerCase().includes(search_value.value.toLowerCase())
      || p.price.toString().includes(search_value.value)
      || p.quantity.toString().includes(search_value.value)
  })
})

function getProducts() {
  axios.get(`${api_url}/products`).then(res => {
    products.value = res.data.data.products
  })
}

function addProduct() {
  axios.post(`${api_url}/products`, product.value).then(res => {
    products.value.push(res.data.data.product)
  })
}

function editProduct() {
  // axios.put(`${api_url}/products/${product.value.id}`, product.value).then(res => {
  //   let edit_product = products.value.find(p => p.id == product.value.id)
  //   edit_product.name = res.data.data.product.name
  //   edit_product.category = res.data.data.product.category
  //   edit_product.price = res.data.data.product.price
  //   edit_product.quantity = res.data.data.product.quantity
  // })
}

function deleteProduct(id) {
  axios.delete(`${api_url}/products/${id}`).then(res => {
    getProducts()
  })
}

getProducts()

</script>

<template>
  <h1>Aplikasi Pengelolaan Produk</h1>

  <div class="form">
    <h2>Form Tambah Dan Edit Produk</h2>
    <div class="input-form">
      <label for="name">Nama</label>
      <input type="text" name="nama" id="nama" v-model="product.name">
    </div>
    <div class="input-form">
      <label for="category">Kategori</label>
      <input type="text" name="category" id="category" v-model="product.category">
    </div>
    <div class="input-form">
      <label for="price">Harga</label>
      <input type="number" name="price" id="price" v-model="product.price">
    </div>
    <div class="input-form">
      <label for="quantity">Jumlah Stok</label>
      <input type="number" name="quantity" id="quantity" v-model="product.quantity">
    </div>
    <div class="input-form">
      <template v-if="product.id == ''">
        <button @click="addProduct" class="btn-success">Tambah Produk</button>
        <button class="btn-danger" type="reset">Reset</button>
      </template>
      <template v-else>
        <button @click="editProduct" class="btn-success">Edit Produk</button>
        <button class="btn-danger" type="button"
          @click="product = { id: '', name: '', category: '', price: 0, quantity: 0, }">Batal</button>
      </template>
    </div>
  </div>

  <div>
    <label for="search">Cari : </label>
    <input type="search" name="search" id="search" v-model="search_value">
  </div>
  <table>
    <thead>
      <tr>
        <th>No</th>
        <th>Nama</th>
        <th>Kategori</th>
        <th>Harga</th>
        <th>Jumlah Stok</th>
        <th>Aksi</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(p, index) in filteredProducts">
        <td>{{ index + 1 }}</td>
        <td>{{ p.name }}</td>
        <td>{{ p.category }}</td>
        <td>{{ p.price }}</td>
        <td>{{ p.quantity }}</td>
        <td>
          <button class="btn-primary"
            @click="product.id = p.id, product.name = p.name, product.category = p.category, product.price = p.price, product.quantity = p.quantity">Edit</button>
          <button class="btn-danger" @click="deleteProduct(p.id)">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
.form {
  border: 1px solid #ccc;
  padding: 1rem;
}

.input-form {
  margin-bottom: 1rem;
}

.input-form label {
  display: block;
  margin-bottom: .25rem;
}

button {
  cursor: pointer;
  padding: .75rem;
  border: 0;
  border-radius: 5px;
  margin-right: 1rem;
}

.btn-success {
  background-color: rgb(36, 190, 87);
  color: white;
}

.btn-danger {
  background-color: rgb(190, 92, 36);
  color: white;
}

.btn-primary {
  background-color: rgb(36, 108, 190);
  color: white;
}

#search {
  margin-top: 1rem;

}

table {
  margin-top: 1rem;
  width: 100%;
}

table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
  text-align: center;
}
</style>
