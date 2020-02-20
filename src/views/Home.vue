<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>New Product Form</h1>
    <div class="new-form">
      Name: <input type="text" v-model="newProductName">
      Descryption: <input type="text" v-model="newProductDescryption">
      Price: <input type="text" v-model="newProductPrice">
      Image: <input type="text" v-model="newProductImageUrl">
    </div>
    <button v-on:click="createProduct()">Create Product</button>
    <div v-for="product in products">
      <img v-on:click="showProduct(product)" v-bind:src="product.image_url" v-bind:alt="product.name" >
      <h2>{{product.name}}</h2>

      <div class="show-page" v-if="product === currentProduct">
      <p>Descryption {{ product.description }}</p>
      <p>Price {{ product.price }}</p>
      <p>Tax {{ product.tax }}</p>
      <p>Total {{ product.total }}</p>

      <div class="edit-form">
        <h4>Edit Product</h4>

        <div>
          Name: <input type="text" v-model="product.name">
        </div>

        <div>
          Descryption: <input type="text" v-model="product.descryption">
        </div>

        <div>
          Price: <input type="text" v-model="product.price">
        </div>

        <div>
          Image URL: <input type="text" v-model="product.image_url">
        </div>
        

        <button v-on:click="updateProduct(product)">Update</button>
        
        <div class="destroy-action">
          <button v-on:click="destroyProduct(product)">Delete</button>
          
        </div>
      </div> <!-- end of .edit-form -->
      </div> <!-- end of .show-page -->
    </div>
    <!-- <p>{{ products }}</p> -->
  </div>
</template>

<style>
</style>

<script>
  let axios = require("axios");
  // import axios from "axios"


export default {
  data: function() {
    return {
      currentProduct: {},
      message: "Welcome to Vue.js!",
      products: [],
      newProductName: "",
      newProductDescryption: "",
      newProductPrice: "",
      newProductImageUrl: "",
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
    });
  },
  methods: {
    createProduct: function() {
      console.log("Product created!")

      let params = {
        name: this.newProductName,
        description: this.newProductDescryption,
        price: this.newProductPrice,
        tax: this.newProductTax,
        total: this.newProductTotal,
        image_url: this.newProductImageUrl
      }

      axios.post("/api/products", params).then(response => {
        // console.log(response.data);
        this.products.push(response.data);
      }).catch(error => console.log(error.response));
    },
    showProduct: function(inputProduct) {
        if (this.currentProduct !== inputProduct) {
          this.currentProduct = inputProduct;
        } else {
          this.currentProduct = {};
        }
       
    },
    updateProduct: function(inputProduct) {
        var clientParams = {
          name: inputProduct.name,
          description: inputProduct.description,
          price: inputProduct.price,
          image_url: inputProduct.image_url
        }
             axios
        .patch("/api/products/" + inputProduct.id, clientParams)
        .then(response => {
          console.log("success", response.data);
        }).catch(error => {
          console.log(error.response.data);
        });

    },
    destroyProduct: function(inputProduct) {
      // console.log("deleteing") ;
          axios
            .delete("/api/products/" + inputProduct.id)
            .then(response => {
              console.log("success", response.data);
              var index = this.products.indexOf(inputProduct);
              this.products.splice(index, 1);
            });
    }
  }
};
</script>

