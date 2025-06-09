<template>
  <div class="AddAccount overlay">
    <form>
      <div class="input">
        <h2>Add Account</h2>
        <div class="field">
          <label for="code">Code</label>
          <input type="text" name="code" id="code" v-model="formValues.code" />
        </div>
        <div class="field">
          <label for="name">Name</label>
          <input type="text" name="name" id="name" v-model="formValues.name" />
        </div>
        <div class="field">
          <label for="phone">Phone</label>
          <input
            type="text"
            name="phone"
            id="phone"
            v-model="formValues.phone"
          />
        </div>
        <div class="field">
          <label for="address">Address</label>
          <input
            type="text"
            name="address"
            id="address"
            v-model="formValues.address"
          />
        </div>
        <div class="field">
          <label for="due">Due</label>
          <input type="text" name="due" id="due" v-model="formValues.due" />
        </div>
        <!-- <div class="errors">{{ validate }}</div> -->
      </div>
      <button @click.prevent="addAccount" class="add">ADD</button>
      <button class="cancel" @click.prevent="closeComponent">CLOSE</button>
    </form>
  </div>
</template>

<style lang="scss" scoped>
.AddAccount {
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
  background-color: #1fdb87;
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
      Accounts: [],
      Inventory: [],
      formValues: {
        code: "",
        name: "",
        phone: "",
        address: "",
        due: +"",
      },
    };
  },
  methods: {
    addAccount: function () {
      // validation
      // Validate form inputs
      if (
        !this.formValues.code ||
        !this.formValues.name ||
        !this.formValues.phone ||
        !this.formValues.address ||
        !this.formValues.due
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
      for (let i = 0; i < this.Accounts.length; i++) {
        if (this.Accounts[i].code == code) {
          return alert("Code must be unique");
        }
      }
      // phone
      let p = Number(this.formValues.phone);
      if (isNaN(p)) {
        return alert("Phone number must be numbers only");
      }
      // due
      let d = Number(this.formValues.due);
      if (isNaN(d)) {
        return alert("Due must be a number");
      }
      // save
      this.Accounts.push({ ...this.formValues });
      this.$emit("update-accounts", this.Accounts); // Emit a 'update-inventory' event to the parent
      localStorage.setItem("Accounts", JSON.stringify(this.Accounts));
      this.$emit("export-data", this.Accounts);
      this.resetForm();
    },
    closeComponent: function () {
      this.$emit("close"); // Emit a 'close' event to the parent
    },
    resetForm: function () {
      this.formValues = {
        code: "",
        name: "",
        phone: "",
        address: "",
        due: 0,
      };
    },
  },
  mounted() {
    const savedInventory = JSON.parse(localStorage.getItem("Inventory"));
    this.Inventory = savedInventory ? savedInventory : []; // Initialize empty array if null
    const savedAccounts = JSON.parse(localStorage.getItem("Accounts"));
    this.Accounts = savedAccounts ? savedAccounts : []; // Initialize empty array if null
  },
};
</script>
