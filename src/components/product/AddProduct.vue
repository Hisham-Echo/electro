<template>
  <div class="AddProduct overlay">
    <form>
      <div class="input">
        <h2>Add Products</h2>
        <div class="field">
          <label for="code">Code</label>
          <input type="text" name="code" id="code" v-model="formValues.code" />
        </div>
        <div class="field">
          <label for="name">Name</label>
          <input type="text" name="name" id="name" v-model="formValues.name" />
        </div>
        <div class="field">
          <label for="name">Category</label>
          <input
            type="text"
            name="category"
            id="category"
            v-model="formValues.category"
          />
        </div>
        <div class="field">
          <label for="quantity">Quantity of Packs</label>
          <input
            type="text"
            name="quantity"
            id="quantity"
            v-model="formValues.quantity"
          />
        </div>
        <div class="field">
          <label for="upp">Units Per Pack</label>
          <input
            type="text"
            name="upp"
            id="upp"
            v-model="formValues.unitsPerPack"
          />
        </div>
        <div class="field">
          <label for="ppp">Price per pack</label>
          <input
            type="text"
            name="ppp"
            id="ppp"
            v-model="formValues.pricePerPack"
          />
        </div>
        <div class="field">
          <label for="ppu">Price per unit</label>
          <input
            type="text"
            name="ppu"
            id="ppu"
            v-model="formValues.pricePerUnit"
          />
        </div>
        <div class="field">
          <label for="vendor">Vendor</label>
          <input
            type="text"
            name="vendor"
            id="vendor"
            v-model="formValues.vendor"
          />
        </div>
      </div>
      <button @click.prevent="addProduct" class="add">ADD</button>
      <button class="cancel" @click.prevent="closeComponent">CLOSE</button>
    </form>
  </div>
</template>

<style lang="scss" scoped>
.AddProduct {
  margin: 0;
  padding: 0;
}

form {
  border-radius: 10px;
  background-color: #80ccff;
  min-width: 450px;
  max-width: 50vw;
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  // align-items: center;
}

input {
  display: flex;
  padding: 5px;
  border: none;
  border-radius: 10px;
}

.input h2 {
  border-radius: 10px 10px 0 0;
  background-color: #22c27a;
  color: white;
  margin: 0;
  padding: 15px;
}

.field {
  padding: 10px;
  display: inline-block;
}

.field label {
  line-height: 1.7;
}

button {
  align-self: flex-end;
  padding: 10px;
  margin: 20px;
  // border: none;
  border: 1px solid #00000066;
  border-radius: 5px;
  justify-self: space-evenly;
}

.add {
  background-color: #1e9f4c;
  color: white;
  font-weight: bold;
}

.cancel {
  color: #ff3c3c;
  font-weight: bold;
  background-color: white;
}

.errors {
  background-color: #ff000022;
  margin: 0 50px;
  color: #f30;
  padding: 10px 0;
}
</style>

<script>
export default {
  name: "AddProduct",
  data() {
    return {
      Inventory: [],
      formValues: {
        code: "",
        name: "",
        category: "",
        quantity: +"",
        unitsPerPack: +"",
        unitQuantity: this.quantity * this.unitsPerPack,
        pricePerPack: +"",
        pricePerUnit: +"",
        vendor: "",
      },
    };
  },
  methods: {
    addProduct: function () {
      // validation
      // Validate form inputs
      if (
        !this.formValues.code ||
        !this.formValues.name ||
        !this.formValues.category ||
        !this.formValues.quantity ||
        !this.formValues.unitsPerPack ||
        !this.formValues.pricePerPack ||
        !this.formValues.pricePerUnit ||
        !this.formValues.vendor
      ) {
        return alert("You must fill all fields.");
      }
      // code
      let code = Number(this.formValues.code);
      // validate its a number
      if (isNaN(code)) {
        return alert("Code must be numbers only");
      }
      // validate its unique
      for (let i = 0; i < this.Inventory.length; i++) {
        if (this.Inventory[i].code == code) {
          return alert("Code must be unique");
        }
      }
      // quantity
      let q = Number(this.formValues.quantity);
      let upp = Number(this.formValues.unitsPerPack);
      if (isNaN(q) || isNaN(upp)) {
        return alert("Quantity must be a number");
      }
      this.formValues.unitQuantity =
        Number(this.formValues.quantity) * Number(this.formValues.unitsPerPack);
      // price per pack & unit
      let ppp = Number(this.formValues.pricePerPack);
      let ppu = Number(this.formValues.pricePerUnit);
      if (isNaN(ppp) || isNaN(ppu)) {
        return alert("Prices must be numbers only");
      }
      // save
      this.Inventory.push({ ...this.formValues });
      this.$emit("update-inventory", this.Inventory); // Emit a 'update-inventory' event to the parent
      localStorage.setItem("Inventory", JSON.stringify(this.Inventory));
      this.$emit("export-data", this.Inventory);
      this.resetForm();
      alert("Product added successfully");
    },
    closeComponent: function () {
      this.$emit("close"); // Emit a 'close' event to the parent
    },
    resetForm: function () {
      this.formValues = {
        code: "",
        name: "",
        category: "",
        quantity: "",
        pricePerPack: "",
        pricePerUnit: "",
        vendor: "",
      };
    },
  },
  mounted() {
    const savedInventory = JSON.parse(localStorage.getItem("Inventory"));
    this.Inventory = savedInventory ? savedInventory : []; // Initialize as an empty array if null
  },
};
</script>
