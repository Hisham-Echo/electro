<template>
  <div class="accounts">
    <h1>ACCOUNTS</h1>
    <div class="functions">
      <button class="add" @click.prevent="toggleChildComponent">
        Add Account
      </button>
      <button class="edit" @click.prevent="toggleChildComponent">
        Edit Account
      </button>
      <button class="pay" @click.prevent="toggleChildComponent">
        Process Payment
      </button>
      <button class="search" @click.prevent="toggleChildComponent">
        Search Account
      </button>
      <button class="del" @click.prevent="toggleChildComponent">
        Delete Account
      </button>
    </div>
    <!-- Forms -->
    <AddAccount
      :Accounts="Accounts"
      v-if="isAddAccountVisible"
      @update-accounts="updateAccounts"
      @export-data="exportData"
      @edit="editAccount"
      @close="toggleChildComponent"
    />
    <EditAccount
      :selectedAccount="currentAccount"
      :Accounts="Accounts"
      v-if="isEditAccountVisible"
      @update-accounts="updateAccounts"
      @export-data="exportData"
      @close="toggleChildComponent"
    />
    <PayCom
      :Accounts="Accounts"
      v-if="isPayVisible"
      @update-accounts="updateAccounts"
      @export-data="exportData"
      @close="toggleChildComponent"
    />
    <SearchAccount
      :Accounts="Accounts"
      v-if="isSearchAccountVisible"
      @close="toggleChildComponent"
    />
    <DeleteAccount
      :Accounts="Accounts"
      v-if="isDeleteAccountVisible"
      @update-accounts="updateAccounts"
      @export-data="exportData"
      @close="toggleChildComponent"
    />
    <!-- start display -->
    <div class="display">
      <table>
        <thead>
          <tr>
            <th>Code</th>
            <th>Name</th>
            <th>Phone</th>
            <th>Address</th>
            <th>Due</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in this.Accounts"
            :key="index"
            @dblclick="handleRowClick(item)"
          >
            <td>{{ item.code }}</td>
            <td>{{ item.name }}</td>
            <td>{{ item.phone }}</td>
            <td>{{ item.address }}</td>
            <td>$ {{ item.due }}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="3">Total Due</td>
            <td colspan="2">$ {{ calcTotalDue() }}</td>
          </tr>
        </tfoot>
      </table>
    </div>
    <!-- end display -->
  </div>
</template>

<style lang="scss" scoped>
.form {
  padding: 10px;
}

button {
  font-weight: bold;
  padding: 10px;
  border: none;
  margin: 0 5px;
  width: 150px;
  cursor: pointer;
  opacity: 0.7;
  transition: opacity 0.2s ease-in-out;
  border-radius: 5px;
  color: white;
  &:hover {
    opacity: 1;
  }

  &:first-of-type {
    background-color: #1cb36f;
  }

  &:nth-of-type(2) {
    background-color: #1f80c0;
  }

  &:nth-of-type(3) {
    background-color: #1423aa;
  }

  &:nth-of-type(4) {
    background-color: #9eaf01;
  }

  &:last-of-type {
    background-color: #fd5656;
  }
}

header {
  font-weight: bold;
  text-transform: capitalize;
}

.display,
header {
  width: 100%;
}

table {
  width: 100%;
  border: 1px solid red;
  padding: 10px;
  border: none;
}

th {
  background-color: #17f893;
}

td {
  background-color: #17f89322;
}

td,
th {
  border: 1px solid transparent;
  padding: 10px 5px;
}

tr:hover td {
  background-color: #17f89355;
  transition: background-color 0.2s ease-in-out;
}

tfoot {
  background-color: #17f89399;
  font-weight: bold;
}

.overlay {
  width: 100%;
  height: 100vh;
  position: fixed;
  background-color: #00000077;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: grid;
  justify-content: center;
  align-content: center;
}
</style>

<script>
// @ is an alias to /src
import AddAccount from "@/components/account/AddAccount.vue";
import EditAccount from "@/components/account/EditAccount.vue";
import SearchAccount from "@/components/account/SearchAccount.vue";
import DeleteAccount from "@/components/account/DeleteAccount.vue";
import PayCom from "@/components/account/PayCom.vue";

export default {
  name: "AccountsView",
  data() {
    return {
      // ## main vars ##
      Inventory: [],
      Accounts: [],
      currentAccount: null, // Holds the currently selected account
      // ###############
      TotalDue: "",
      isAddAccountVisible: false,
      isEditAccountVisible: false,
      isSearchAccountVisible: false,
      isDeleteAccountVisible: false,
      isPayVisible: false,
    };
  },
  components: {
    AddAccount,
    EditAccount,
    SearchAccount,
    DeleteAccount,
    PayCom,
  },
  methods: {
    editAccount(selectedAccount) {
      this.currentAccount = selectedAccount;
      this.isSearchAccountVisible = false;
      this.isEditAccountVisible = true;
    },
    toggleChildComponent: function (event) {
      if (event == undefined) {
        // close opened component
        this.isAddAccountVisible = false;
        this.isEditAccountVisible = false;
        this.isSearchAccountVisible = false;
        this.isDeleteAccountVisible = false;
        this.isPayVisible = false;
      } else if (event.target.classList.contains("add")) {
        this.isAddAccountVisible = !this.isAddAccountVisible; // Toggle add visibility
      } else if (event.target.classList.contains("edit")) {
        this.isEditAccountVisible = !this.isEditAccountVisible; // Toggle edit visibility
      } else if (event.target.classList.contains("search")) {
        this.isSearchAccountVisible = !this.isSearchAccountVisible; // Toggle search visibility
      } else if (event.target.classList.contains("del")) {
        this.isDeleteAccountVisible = !this.isDeleteAccountVisible; // Toggle delete visibility
      } else if (event.target.classList.contains("pay")) {
        this.isPayVisible = !this.isPayVisible; // Toggle pay visibility
      }
    },
    calcTotalDue: function () {
      let sum = 0;
      for (let i = 0; i < this.Accounts.length; i++) {
        sum += Number(this.Accounts[i].due);
      }
      this.TotalDue = sum;
      return sum;
    },
    // Save localStorage to a file
    exportData() {
      const localStorageData = {};
      for (let i = 0; i < localStorage.length; i++) {
        const key = localStorage.key(i);
        const value = localStorage.getItem(key);
        localStorageData[key] = value;
      }

      const blob = new Blob([JSON.stringify(localStorageData, null, 2)], {
        type: "application/json",
      });
      const url = URL.createObjectURL(blob);

      const a = document.createElement("a");
      a.href = url;
      const date = new Date();
      a.download = `DATA@${date.toISOString()}.json`; // File name
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);

      URL.revokeObjectURL(url);
    },
    updateAccounts(newAccounts) {
      this.Accounts = newAccounts;
      localStorage.setItem("Accounts", JSON.stringify(this.Accounts));
    },
    handleRowClick(account) {
      this.currentAccount = account; // Set the selected product
      this.isEditAccountVisible = true; // Open the EditProduct component
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
