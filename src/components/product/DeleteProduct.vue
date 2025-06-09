<template>
  <div class="delProduct overlay">
    <form>
      <div class="input">
        <h2>Delete Products</h2>
        <div class="field">
          <label for="code">Code</label>
          <input type="text" name="code" id="code" v-model="formValues.code" />
        </div>
      </div>
      <div>
        <button @click.prevent="delProduct" class="delete">DELETE</button>
        <button class="cancel" @click.prevent="closeComponent">CLOSE</button>
      </div>
    </form>
  </div>
</template>

<style lang="scss" scoped>
form {
  border-radius: 10px;
  background-color: #80ccff;
  min-width: 450px;
  max-width: 50vw;
  display: flex;
  flex-flow: column nowrap;
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
  background-color: #fd6363;
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
  border: 1px solid #00000066;
  border-radius: 5px;
  justify-self: space-evenly;
}

.delete {
  background-color: #fd5656;
  color: white;
  font-weight: bold;
}

.cancel {
  color: #ff3c3c;
  font-weight: bold;
  background-color: white;
}
</style>

<script>
export default {
  name: "DeleteProduct",
  data() {
    return {
      Inventory: [],
      formValues: {
        code: "",
      },
    };
  },
  methods: {
    delProduct: function () {
      for (let i = 0; i < this.Inventory.length; i++) {
        // find product
        if (this.Inventory[i].code == this.formValues.code) {
          // generate message
          let product = ``;
          Object.keys(this.Inventory[i]).forEach((key) => {
            product += `${key}: ${this.Inventory[i][key]} \n`;
          });
          // confirm delete
          const flag = confirm(
            `Are u sure you want to delete this product \n${product}`
          );
          // true => delete and save data
          if (flag) {
            this.Inventory.splice(i, 1);
            this.$emit("update-inventory", this.Inventory); // Emit a 'update-inventory' event to the parent
            localStorage.setItem("Inventory", JSON.stringify(this.Inventory));
            this.$emit("export-data", this.Inventory); // Emit a 'export-data' event to the parent
            this.resetForm();
            break;
          } // false => stop operation
          else {
            break;
          }
        } else {
          continue;
        }
      }
    },
    closeComponent: function () {
      this.$emit("close"); // Emit a 'close' event to the parent
    },
    resetForm: function () {
      this.formValues = {
        code: "",
      };
    },
  },
  mounted() {
    const savedInventory = JSON.parse(localStorage.getItem("Inventory"));
    this.Inventory = savedInventory ? savedInventory : []; // Initialize as an empty array if null
  },
};
</script>
