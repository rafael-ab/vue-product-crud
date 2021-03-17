<template>
  <v-app>
    <v-container fill-height fluid>
      <v-row>
        <v-col cols="12">
          <v-btn
            class="d-block d-sm-none btn-xs-fab"
            color="blue white--text"
            @click="newItem"
            fab
          >
            <v-icon large>mdi-plus</v-icon>
          </v-btn>
          <v-data-table
            class="pt-5 px-3 elevation-5"
            item-key="code"
            height="500"
            :items="products"
            :headers="headers"
            :search="search"
            :footer-props="{
              'items-per-page-options': [5, 15, 30, 50],
            }"
            :options="{
              itemsPerPage: 15,
            }"
            fixed-header
          >
            <template v-slot:top>
              <v-text-field
                v-model="search"
                class="mx-5 mb-5 d-block d-md-none elevation-1"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details
                filled
              ></v-text-field>
              <v-toolbar flat>
                <v-toolbar-title>Products</v-toolbar-title>
                <v-divider class="mx-4" inset vertical></v-divider>
                <v-spacer></v-spacer>
                <v-btn
                  class="d-none d-sm-block"
                  color="blue white--text"
                  @click="newItem"
                >
                  New Product
                </v-btn>
                <v-text-field
                  v-model="search"
                  class="mx-5 d-none d-md-block"
                  append-icon="mdi-magnify"
                  label="Search"
                  single-line
                  hide-details
                ></v-text-field>
              </v-toolbar>
              <ProductManagerModal
                :productManagerModal="productManagerModal"
                :productManagerModalTitle="productManagerModalTitle"
                :productInfo="product"
                @toggleProductManagerModal="toggleProductManagerModal($event)"
                @saveProduct="saveProduct($event)"
              />
              <ProductDeleteModal
                :productDeleteModal="productDeleteModal"
                :productInfo="product"
                @toggleProductDeleteModal="toggleProductDeleteModal($event)"
                @deleteProductConfirmed="deleteProduct($event)"
              />
            </template>
            <template v-slot:item.price="{ item }">
              <v-icon class="mr-2" small> mdi-currency-usd </v-icon>
              {{ item.price }}
            </template>
            <template v-slot:item.actions="{ item }">
              <v-icon class="mr-2" color="amber" @click="editItem(item)">
                mdi-pencil
              </v-icon>
              <v-icon color="red" @click="deleteItem(item)">
                mdi-delete
              </v-icon>
            </template>
            <template v-slot:no-data>
              <v-btn color="primary" @click="initialize"> Reset </v-btn>
            </template>
          </v-data-table>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import ProductManagerModal from "./components/ProductManagerModal.vue";
import ProductDeleteModal from "./components/ProductDeleteModal.vue";
import faker from "faker";

export interface Product {
  code: string;
  name: string;
  price: number;
  qty: number;
}

export default Vue.extend({
  name: "App",

  methods: {
    newItem(): void {
      this.productManagerModal = true;
      this.productManagerModalTitle = "New Product";
    },
    editItem(item: Product): void {
      this.productManagerModal = true;
      this.productManagerModalTitle = "Edit Product";
      this.product = {
        code: item.code,
        name: item.name,
        price: item.price,
        qty: item.qty,
      };
    },
    deleteItem(item: Product): void {
      this.productDeleteModal = true;
      this.product = {
        code: item.code,
        name: item.name,
        price: item.price,
        qty: item.qty,
      };
    },
    saveProduct(product: Product): void {
      const index: number = this.getProductIndexByCode(product.code);
      if (index > -1) {
        Object.assign(this.products[index], product);
        /* this.$set(this.products[index], "code", product.code);
        this.$set(this.products[index], "name", product.name);
        this.$set(this.products[index], "price", product.price);
        this.$set(this.products[index], "qty", product.qty); */
      } else {
        this.products.unshift(product);
      }
    },
    deleteProduct(product: Product): void {
      const index: number = this.getProductIndexByCode(product.code);
      this.products.splice(index, 1);
    },
    toggleProductManagerModal(state: boolean): void {
      if (!state) {
        this.resetProduct();
      }
      this.productManagerModal = state;
    },
    toggleProductDeleteModal(state: boolean): void {
      if (!state) {
        this.resetProduct();
      }
      this.productDeleteModal = state;
    },
    getProductIndexByCode(productCode: string): number {
      for (const [index, product] of this.products.entries()) {
        if (product.code == productCode) {
          return index;
        }
      }
      return -1;
    },
    resetProduct(): void {
      this.product = {
        code: "",
        name: "",
        price: 0.0,
        qty: 0,
      };
    },
    initialize(): void {
      for (let i = 0; i < 100; i++) {
        this.products.unshift({
          code: faker.random.uuid(),
          name: faker.commerce.productName(),
          price: faker.random.float(),
          qty: faker.random.number(),
        });
      }
    },
  },

  components: {
    ProductManagerModal,
    ProductDeleteModal,
  },

  created() {
    this.initialize();
  },

  data() {
    return new (class {
      search = "";
      productManagerModal = false;
      productDeleteModal = false;
      productManagerModalTitle = "";
      headers: Array<object> = [
        { text: "Code", value: "code" },
        { text: "Name", value: "name" },
        { text: "Price", value: "price" },
        { text: "Qty", value: "qty" },
        { text: "Actions", value: "actions", sortable: false },
      ];
      product: Product = {
        code: "",
        name: "",
        price: 0,
        qty: 0,
      };
      products: Product[] = [];
    })();
  },
});
</script>

<style scoped>
#app {
  background: #4e54c8; /* fallback for old browsers */
  background: -webkit-linear-gradient(
    to right,
    #8f94fb,
    #4e54c8
  ); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(
    to right,
    #8f94fb,
    #4e54c8
  ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}
.btn-xs-fab {
  position: absolute;
  top: 15%;
  right: 30px;
  z-index: 3;
}
</style>
