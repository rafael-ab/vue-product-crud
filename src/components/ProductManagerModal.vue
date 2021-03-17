<template>
  <v-dialog v-model="productManagerModalState" max-width="80%">
    <v-card>
      <v-card-title>
        <span class="text-h5">{{ productManagerModalTitle }}</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-text-field
                v-model="product.code"
                label="Code"
                :error-messages="codeErrors"
                :success="!$v.product.code.$invalid"
                @input="$v.product.code.$touch()"
                @blur="$v.product.code.$touch()"
                required
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                v-model="product.name"
                label="Name"
                :error-messages="nameErrors"
                :success="!$v.product.name.$invalid"
                @input="$v.product.name.$touch()"
                @blur="$v.product.name.$touch()"
                required
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                v-model.number="product.price"
                label="Price"
                type="number"
                :error-messages="priceErrors"
                :success="!$v.product.price.$invalid"
                @input="$v.product.price.$touch()"
                @blur="$v.product.price.$touch()"
                required
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                v-model.number="product.qty"
                label="Qty"
                type="number"
                :error-messages="qtyErrors"
                :success="!$v.product.qty.$invalid"
                @input="$v.product.qty.$touch()"
                @blur="$v.product.qty.$touch()"
                required
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="red darken-1" @click="closeModal" text> Cancel </v-btn>
        <v-btn color="blue darken-1" @click="validate" outlined> Save </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch, Emit } from "vue-property-decorator";
import { Product } from "../App.vue";
import { validationMixin } from "vuelidate";
import { required } from "vuelidate/lib/validators";

@Component({
  name: "ProductManagerModal",
  mixins: [validationMixin],
  validations: {
    product: {
      code: {
        required,
      },
      name: {
        required,
      },
      price: {
        required,
        higherThanZero: (price: number) => price > 0,
      },
      qty: {
        required,
        higherThanZero: (qty: number) => qty > 0,
      },
    },
  },
})
export default class ProductManagerModal extends Vue {

  validate(): void {
    this.$v.$touch();
    if (!this.$v.$invalid) {
      this.save();
      this.closeModal();
    }
  }

  @Emit("saveProduct")
  save(): Product {
    const product: Product = {
      code: this.product.code,
      name: this.product.name,
      price: this.product.price,
      qty: this.product.qty,
    };
    return product;
  }

  @Emit("toggleProductManagerModal")
  closeModal(): boolean {
    return false;
  }

  get codeErrors(): Array<string> {
    const errors: string[] = [];
    if (!this.$v.product.code.$dirty) return errors;
    !this.$v.product.code.required && errors.push("Field required.");
    return errors;
  }

  get nameErrors(): string[] {
    const errors: string[] = [];
    if (!this.$v.product.name.$dirty) return errors;
    !this.$v.product.name.required && errors.push("Field required.");
    return errors;
  }

  get priceErrors(): string[] {
    const errors: string[] = [];
    if (!this.$v.product.price.$dirty) return errors;
    !this.$v.product.price.required && errors.push("Field required.");
    !this.$v.product.price.higherThanZero &&
      errors.push("Field must be higher than 0.");
    return errors;
  }

  get qtyErrors(): string[] {
    const errors: string[] = [];
    if (!this.$v.product.qty.$dirty) return errors;
    !this.$v.product.qty.required && errors.push("Field required.");
    !this.$v.product.qty.higherThanZero &&
      errors.push("Field must be higher than 0.");
    return errors;
  }

  get productManagerModalState(): boolean {
    return this.productManagerModal;
  }
  set productManagerModalState(state: boolean) {
    this.productManagerModal = state;
  }

  @Watch("productInfo")
  setProduct() {
    this.product = {
      code: this.productInfo.code,
      name: this.productInfo.name,
      price: this.productInfo.price,
      qty: this.productInfo.qty,
    };
    this.$v.$reset();
  }

  @Prop() public productManagerModal!: boolean;
  @Prop() public productManagerModalTitle!: string;
  @Prop() public productInfo!: Product;

  private product: Product = {
    code: "",
    name: "",
    price: 0,
    qty: 0,
  };

}
</script>

<style></style>
