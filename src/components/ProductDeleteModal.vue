<template>
  <v-dialog v-model="productDeleteModalState" :max-width="$vuetify.breakpoint.name != 'xs' ? '50%' : '80%'">
    <v-card>
      <v-card-title class="text-h5">Delete Product</v-card-title>
      <v-card-subtitle class="mt-2 text-body-2">Are you sure you want to delete <b>{{ productInfo.code }} {{ productInfo.name }}</b>?</v-card-subtitle>
      <v-card-actions class="d-flex justify-end">
        <v-btn color="grey" @click="closeModal" text>Cancel</v-btn>
        <v-btn color="red darken-1" @click="deleteItemConfirm" outlined>OK</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { Vue, Component, Prop, Emit } from "vue-property-decorator";
import { Product } from "../App.vue";

@Component({
  name: "ProductDeleteModal",
})

export default class ProductDeleteModal extends Vue {

  @Emit("deleteProductConfirmed")
  deleteItemConfirm(): Product {
    this.closeModal();
    return this.productInfo;
  }

  @Emit("toggleProductDeleteModal")
  closeModal(): boolean {
    return false;
  }

  get productDeleteModalState(): boolean {
    return this.productDeleteModal;
  }
  set productDeleteModalState(state: boolean) {
    this.productDeleteModal = state;
  }

  @Prop() public productDeleteModal!: boolean;
  @Prop() public productInfo!: Product;

}

</script>

<style></style>
