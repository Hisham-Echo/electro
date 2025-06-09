<template>
  <div class="SearchAccount overlay">
    <form>
      <div class="input">
        <h2>Search Accounts</h2>
        <div class="field">
          <input
            type="search"
            name="search"
            id="search"
            v-model="searchField"
            placeholder="Search account"
          />
        </div>
      </div>
      <div class="display">
        <table>
          <thead>
            <tr>
              <th>Code</th>
              <th>Name</th>
              <th>Phone</th>
              <th>Due</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(account, index) in filteredAccounts"
              :key="index"
              :class="{ active: isSelected(account) }"
              @click.prevent="toggleActive(account)"
            >
              <td>{{ account.code }}</td>
              <td>{{ account.name }}</td>
              <td>{{ account.phone }}</td>
              <td>{{ account.due }}</td>
            </tr>
          </tbody>
          <tfoot></tfoot>
        </table>
      </div>
      <div>
        <button @click.prevent="editSelectedAccount" class="search">
          SELECT
        </button>
        <button class="cancel" @click.prevent="closeComponent">CLOSE</button>
      </div>
    </form>
  </div>
</template>

<style lang="scss" scoped>
/* width */
::-webkit-scrollbar {
  width: 10px;
}
/* Track */
::-webkit-scrollbar-track {
  border-radius: 0 10px 10px 0;
  background: #f1f1f1;
}
/* Handle */
::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 0 10px 10px 0;
}
/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #555;
}

form {
  border-radius: 10px;
  background-color: #80ccff;
  min-width: 500px;
  max-width: 100vw;
  height: 50vh;
  display: flex;
  flex-flow: column nowrap;
  justify-content: space-between;
  // align-items: center;
}

input {
  display: flex;
  padding: 5px;
  border: none;
  border-radius: 10px;
  width: 100%;
}

.input h2 {
  border-radius: 10px 10px 0 0;
  background-color: #e2f817;
  margin: 0;
  padding: 15px;
}

.field {
  // width: 100%;
  padding: 10px;
  // display: inline-block;
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

.search {
  background-color: #e2f817;
  // color: white;
  font-weight: bold;
}

.cancel {
  color: #ff3c3c;
  font-weight: bold;
  background-color: white;
}
.active {
  background-color: #e2f817dd;
}
// table
.display {
  width: calc(100% - 20px);
  overflow-y: scroll;
  padding: 0 0 0 10px;
}

table {
  width: 100%;
  border: 1px solid red;
  border: none;
  border-radius: 10px;
  height: 60%;
  overflow-y: scroll;
}

th {
  background-color: #17f893;
}

td {
  background-color: transparent;
}

td,
th {
  border: 1px solid transparent;
  padding: 10px 5px;
}

tr {
  background-color: white;
}

tr:hover {
  cursor: pointer;
  background-color: #a9f817aa;
  transition: background-color 0.2s ease-in-out;
  &.active {
    background-color: #e2f817dd;
  }
}
</style>

<script>
export default {
  name: "SearchAccount",
  data() {
    return {
      Accounts: [],
      searchField: "",
      selected: [],
      selectedAccount: null,
    };
  },
  props: {
    Account: Array,
  },
  methods: {
    toggleActive(account) {
      const index = this.selected.findIndex(
        (selectedItem) => selectedItem.code === account.code
      );
      if (index > -1) {
        this.selected.splice(index, 1); // Remove from selection
      } else {
        this.selected = [account]; // Only one account selected at a time for editing
      }
    },
    selectAccount(account) {
      this.selectedAccount = account;
    },
    // isSelected(account) {
    //   return this.selectedAccount?.code === account.code;
    // },
    confirmSelection() {
      if (!this.selectedAccount) {
        alert("Please select an account.");
        return;
      }
      this.$emit("edit", this.selectedAccount);
    },
    isSelected(account) {
      return this.selected.length > 0 && this.selected[0].code === account.code;
    },
    editSelectedAccount() {
      if (this.selected.length === 0) {
        alert("Please select an account to edit.");
        return;
      }

      this.$emit("edit", this.selected[0]); // Emit the selected account for editing
    },
    closeComponent() {
      this.$emit("close"); // Emit a close event to the parent
    },
    resetForm() {
      this.searchField = "";
      this.selected = [];
    },
  },
  computed: {
    filteredAccounts() {
      if (!this.searchField) return this.Accounts;

      const searchQuery = this.searchField.toLowerCase();

      return this.Accounts.filter(
        (account) =>
          account.code.toLowerCase().includes(searchQuery) ||
          account.name.toLowerCase().includes(searchQuery) ||
          account.phone.toLowerCase().includes(searchQuery)
      );
    },
  },
  // methods: {
  //   toggleActive(account) {
  //     const index = this.selected.findIndex(
  //       (selectedItem) => selectedItem.code === account.code
  //     );
  //     if (index > -1) {
  //       this.selected.splice(index, 1); // Remove from selection
  //     } else {
  //       this.selected.push(account); // Add to selection
  //     }
  //   },
  //   isSelected(account) {
  //     return this.selected.some(
  //       (selectedItem) => selectedItem.code === account.code
  //     );
  //   },
  //   searchAccount() {
  //     this.$emit("select", this.selected); // Emit selected accounts to the parent
  //   },
  //   closeComponent() {
  //     this.$emit("close"); // Emit a close event to the parent
  //   },
  //   resetForm() {
  //     this.searchField = "";
  //     this.selected = [];
  //   },
  // },
  mounted() {
    const savedAccounts = JSON.parse(localStorage.getItem("Accounts"));
    this.Accounts = savedAccounts ? savedAccounts : []; // Load accounts from localStorage
  },
};
</script>
