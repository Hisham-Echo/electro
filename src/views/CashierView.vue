<template>
  <div class="cashier">
    <h1>CASHIER</h1>
    <form>
      <div class="display">
        <header>
          <div class="cCode">
            <label for="c_code">Customer Code : </label>
            <input
              type="text"
              name="c_code"
              id="c_code"
              v-model="customerCode"
              @click.prevent
            />
          </div>
          <div class="customer">{{ getCustomerName() }}</div>
          <div class="precart">
            <div class="paymentMethod">
              <span>Payment Method: </span>
              <select v-model="paymentMethod" id="payment-method">
                <option value="cash">Cash</option>
                <option value="2agel">2agel</option>
              </select>
            </div>
            <div class="productCode">
              <label for="p_code">Product Code : </label>
              <input
                type="text"
                name="p_code"
                id="p_code"
                v-model="productCode"
                placeholder="product code"
              />
              <button @click.prevent="addToCart">Add to Cart</button>
            </div>
            <div class="productType">
              <span>Product Type: </span>
              <select v-model="productType" id="productType">
                <option value="gomla">Gomla</option>
                <option value="2ata3y">2ata3y</option>
              </select>
            </div>
          </div>
        </header>

        <!-- Cart -->
        <main>
          <div class="cart">
            <table>
              <thead>
                <tr>
                  <th>Code</th>
                  <th>Name</th>
                  <th>Category</th>
                  <th>Quantity</th>
                  <th>Price per pack</th>
                  <th>Price per unit</th>
                  <th>Total</th>
                  <th>Stock</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in cart" :key="index">
                  <td :id="item.code">
                    <button
                      class="remove"
                      @click.prevent="removeFromCart"
                      title="remove from cart"
                    >
                      X
                    </button>
                    {{ item.code }}
                  </td>
                  <td>{{ item.name }}</td>
                  <td>{{ item.category }}</td>
                  <td>
                    <input
                      type="number"
                      v-model="item.q"
                      :min="1"
                      :max="
                        productType === 'gomla'
                          ? item.stockPackages
                          : item.stockUnits
                      "
                      @change="updateCart()"
                    />
                  </td>
                  <td>$ {{ item.pricePerPack }}</td>
                  <td>$ {{ item.pricePerUnit }}</td>
                  <td>
                    $
                    {{
                      (
                        item.q *
                        (productType === "gomla"
                          ? item.pricePerPack
                          : item.pricePerUnit)
                      ).toFixed(2)
                    }}
                  </td>
                  <td>
                    {{
                      productType === "gomla"
                        ? item.stockPackages
                        : item.stockUnits
                    }}
                  </td>
                </tr>
              </tbody>
              <tfoot>
                <tr v-if="productType == 'gomla'">
                  <td colspan="6">Total</td>
                  <td colspan="2">${{ calcTotalPrice.toFixed(2) }}</td>
                </tr>
                <tr v-else>
                  <td colspan="6">Total</td>
                  <td colspan="2">${{ calcTotalPrice2.toFixed(2) }}</td>
                </tr>
              </tfoot>
            </table>
          </div>
        </main>

        <!-- Total and Payment -->
        <footer>
          <div class="total-section">
            <p v-if="productType == 'gomla'">
              Total: ${{ calcTotalPrice.toFixed(2) }}
            </p>
            <p v-else>Total: ${{ calcTotalPrice2.toFixed(2) }}</p>
            <div v-if="paymentMethod === 'cash'">
              <label for="amount-paid">Amount Paid: </label>
              <input
                type="number"
                id="amount-paid"
                name="amount-paid"
                v-model="amountPaid"
                placeholder="Enter amount"
              />
              <h4 v-if="productType == 'gomla'">
                Change: ${{ (amountPaid - calcTotalPrice).toFixed(2) }}
              </h4>
              <h4 v-else-if="productType == '2ata3y'">
                Change: ${{ (amountPaid - calcTotalPrice2).toFixed(2) }}
              </h4>
            </div>
          </div>
          <div class="sell">
            <button @click.prevent="sell">SELL</button>
          </div>
        </footer>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "CashierView",
  data() {
    return {
      Accounts: [],
      Inventory: [],
      Dorg: 0,
      //
      customerCode: "0",
      customerName: "",
      productCode: "",
      //
      cart: [],
      totalPrice: 0,
      paymentMethod: "cash",
      productType: "gomla",
      amountPaid: 0,
    };
  },
  computed: {
    // Calculate the total price of the cart
    calcTotalPrice() {
      return this.cart.reduce((total, item) => {
        return total + item.pricePerPack * item.q;
      }, 0);
    },
    calcTotalPrice2() {
      return this.cart.reduce((total, item) => {
        return total + item.pricePerUnit * item.q;
      }, 0);
    },
  },
  methods: {
    addToCart() {
      if (!this.productCode) {
        alert("Please enter product code");
        return;
      }

      const product = this.Inventory.find(
        (item) => item.code === this.productCode
      );

      if (!product) {
        alert("Product not found in inventory");
        return;
      }

      const existingItem = this.cart.find((item) => item.code === product.code);

      if (existingItem) {
        if (this.productType == "gomla") {
          if (existingItem.q < product.quantity) {
            existingItem.q++; // Add one package
          } else {
            alert("Not enough packages in stock");
          }
        } else {
          if (existingItem.q < product.unitQuantity) {
            existingItem.q++; // Add one unit
          } else {
            alert("Not enough units in stock");
          }
        }
      } else {
        const newItem = {
          ...product,
          q: 1,
          stockPackages: product.quantity, // Keep track of original stock
          stockUnits: product.unitQuantity,
        };
        this.cart.push(newItem);
      }

      localStorage.setItem("Cart", JSON.stringify(this.cart));
      this.productCode = ""; // Reset input
    },
    removeFromCart(event) {
      // remove item from cart
      let id = event.target.parentElement.id;
      this.cart = this.cart.filter((item) => item.code !== id);
      localStorage.setItem("Cart", JSON.stringify(this.cart)); // Update storage
    },
    sell() {
      // TODO:
      // take each quantity of sold product and subtract it from the quantity in inventory
      const check = confirm("Confirm Sell");
      if (check) {
        if (this.cart.length === 0) {
          alert("Cart is empty. Please add products before selling.");
          return;
        }
        // Loop through cart and update inventory
        for (let i = 0; i < this.cart.length; i++) {
          const cartItem = this.cart[i];
          const inventoryItem = this.Inventory.find(
            (item) => item.code === cartItem.code
          );

          if (inventoryItem) {
            if (this.productType === "gomla") {
              // Selling by package
              if (inventoryItem.quantity >= cartItem.q) {
                inventoryItem.quantity -= cartItem.q;
                inventoryItem.unitQuantity -=
                  cartItem.q * inventoryItem.unitsPerPack;
              } else {
                alert(`Not enough stock for ${inventoryItem.name}`);
                return;
              }
            } else {
              // Selling by unit
              if (inventoryItem.unitQuantity >= cartItem.q) {
                inventoryItem.unitQuantity -= cartItem.q;
                inventoryItem.quantity = Math.floor(
                  inventoryItem.unitQuantity / inventoryItem.unitsPerPack
                );
              } else {
                alert(`Not enough stock for ${inventoryItem.name}`);
                return;
              }
            }
          }
        }

        // Update local storage with the new inventory data
        localStorage.setItem("Inventory", JSON.stringify(this.Inventory));

        // Handle payment logic
        if (this.paymentMethod === "cash") {
          alert(`Transaction completed!`);
          // add to dorg
          this.Dorg = this.calcTotalPrice.toFixed(2);
          localStorage.setItem("Dorg", this.Dorg);
        } else if (this.paymentMethod === "2agel") {
          const customer = this.Accounts.find(
            (acc) => acc.code === this.customerCode
          );
          if (customer) {
            customer.due = (customer.due || 0) + this.calcTotalPrice;
            localStorage.setItem("Accounts", JSON.stringify(this.Accounts));
            alert(
              `Sale recorded on credit for ${
                customer.name
              }. Due amount: $${customer.due.toFixed(2)}`
            );
          }
        }

        // empty cart
        this.cart = [];
        localStorage.setItem("Cart", JSON.stringify(this.cart));
        this.exportData();
      }
    },
    getCustomerName() {
      const customer = this.Accounts.find(
        (account) => account.code === this.customerCode
      );
      return customer ? customer.name : "Customer not found";
    },

    updateInventory(newInventory) {
      this.Inventory = newInventory;
      localStorage.setItem("Inventory", JSON.stringify(this.Inventory));
    },

    updateAccounts(newAccounts) {
      this.Accounts = newAccounts;
      localStorage.setItem("Accounts", JSON.stringify(this.Accounts));
    },

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
    updateCart() {
      // Update the cart when quantity changes
      localStorage.setItem("Cart", JSON.stringify(this.cart));
    },
  },
  mounted() {
    const savedInventory = JSON.parse(localStorage.getItem("Inventory"));
    this.Inventory = savedInventory ? savedInventory : [];
    const savedAccounts = JSON.parse(localStorage.getItem("Accounts"));
    this.Accounts = savedAccounts ? savedAccounts : [];
    const savedCart = JSON.parse(localStorage.getItem("Cart"));
    this.cart = savedCart ? savedCart : [];
    const savedDorg = JSON.parse(localStorage.getItem("Dorg"));
    this.Dorg = savedDorg ? savedDorg : 0;
  },
};
</script>

<style lang="scss" scoped>
header {
  background-color: #0000ff11;
  margin: 0 10px;
  padding: 20px 10px;
  .cCode {
    background-color: #17f89322;
    margin: 0 15px;
    padding: 10px;
  }
  #c_code {
    width: 50px;
  }
  .customer {
    background-color: #17f89356;
    margin: 0 15px;
    padding: 10px;
    font-weight: bold;
  }
  .precart {
    background-color: #17f8931a;
    padding: 5px 20px;
    margin: 0 15px;
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
    align-items: center;
  }
  .productCode {
    // background-color: red;
    padding: 10px;
    // text-align: left;
    #p_code {
      margin-right: 10px;
      padding: 5px;
      width: 100px;
    }
    button {
      background-color: #17f86255;
      font-weight: bold;
      border: none;
      border-radius: 5px;
      padding: 10px;
      transition: background 0.2s ease-in-out;
      &:hover {
        background-color: #17ff62aa;
      }
    }
  }
}
table {
  width: 100%;
  margin: 0;
  padding: 10px;
  border: none;
  max-height: 65vh;
  overflow-x: auto;
}

th,
tfoot {
  background-color: #17f893;
  &:nth-of-type(1) {
    width: 12%;
  }
  &:nth-of-type(2) {
    width: 22%;
  }
  &:nth-of-type(3) {
    width: 10%;
  }
  &:nth-of-type(4),
  &:nth-of-type(5),
  &:nth-of-type(6),
  &:nth-of-type(7),
  &:nth-of-type(8) {
    width: 8%;
  }
}

td {
  position: relative;
  background-color: #17f89322;
  input {
    width: 50%;
    padding: 5px;
    border: none;
    outline: none;
  }
}

td,
th {
  border: 1px solid transparent;
  padding: 10px 5px;
}

tfoot {
  font-weight: bold;
}

footer {
  margin: 0 10px;
  padding: 10px 20px;
  margin-top: 0px;
  display: flex;
  flex-flow: row nowrap;
  justify-content: space-between;
  align-items: center;
  background-color: #0000ff11;
  .sell {
    button {
      border-radius: 5px;
      background-color: #ff00008a;
      font-weight: bolder;
      color: white;
      border: none;
      padding: 10px;
      transition: background 0.2s ease-in-out;
      &:hover {
        background-color: red;
      }
    }
  }
}

.total-section {
  padding: 10px;
  margin: 0 10px;
}

.remove {
  font-weight: bold;
  font-size: 0.8rem;
  background-color: #fd565699;
  border: none;
  color: white;
  text-align: center;
  position: absolute;
  left: 10px;
  padding: 1px 4px;
  transition: background 0.2s ease-in-out;
  &:hover {
    background-color: red;
  }
}
</style>
