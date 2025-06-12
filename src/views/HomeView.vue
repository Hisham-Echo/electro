<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to ALRadwan" />
    <button class="load" @click.prevent="triggerFileInput">Load Data</button>
    <input
      type="file"
      ref="fileInput"
      @change="loadLocalStorageFromFile"
      accept=".json"
      style="display: none"
    />
    <div class="dorg">
      <div>
        Dorg: <span>${{ this.Dorg }}</span>
        <br>
        <br>
        Visa: <span>${{ this.Visa }}</span>
        <br>
        <br>
        Net Profit: <span>${{ (this.Dorg + this.Visa) * 0.1 }}</span>
      </div>
      <div>
        <button @click.prevent="resetDorg">Reset</button>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.home img {
  transform: rotate(180deg);
}

.dorg {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-flow: column nowrap;
  gap: 20px;
  font-size: 1.25rem;
  font-weight: bolder;
  margin: 10px;
  padding: 20px;
  // background-color: red;
  span {
    background-color: #0000ff60;
    padding: 2px 10px;
    display: inline-block;
    min-width: 60px;
  }
  button {
    background: red;
  }
}

button {
  font-weight: bold;
  padding: 10px;
  border: none;
  margin: 0 5px;
  width: 150px;
  cursor: pointer;
  opacity: 0.5;
  transition: opacity 0.2s ease-in-out;
  border-radius: 5px;
  color: white;
  background-color: #1423aa;
  &:hover {
    opacity: 1;
  }
}
</style>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  data() {
    return {
      Dorg: 0,
      Visa: 0,
    }
  },
  components: {
    HelloWorld,
  },
  methods: {
    // empty Dorg
    resetDorg() {
      const check = confirm('ARE YOU SURE YOU WANT TO EMPTY DORG ?')
      if (check) {
        this.Dorg = 0
        localStorage.setItem('Dorg', this.Dorg)
      }
    },
    // Trigger the hidden file input for uploading data
    triggerFileInput() {
      this.$refs.fileInput.click() // Opens the file input dialog
    },
    // Load data into localStorage from a file
    async loadLocalStorageFromFile(event) {
      const file = event.target.files[0]
      if (!file) return

      const reader = new FileReader()
      reader.onload = () => {
        try {
          const data = JSON.parse(reader.result)

          // Populate localStorage with the data
          Object.keys(data).forEach((key) => {
            localStorage.setItem(key, data[key])
          })

          // Reload inventory data from localStorage if necessary
          this.Inventory = JSON.parse(localStorage.getItem('Inventory')) || []
          alert('Data successfully loaded into localStorage!')
        } catch (error) {
          alert('Invalid file format. Please upload a valid JSON file.',error)
        }
      }
      reader.readAsText(file)
      // Reset the input value to allow re-uploading the same file
      event.target.value = ''
    },
  },
  mounted() {
    const savedDorg = JSON.parse(localStorage.getItem('Dorg'))
    this.Dorg = savedDorg ? savedDorg : 0
    const savedVisa = JSON.parse(localStorage.getItem('Visa'))
    this.Visa = savedVisa ? savedVisa : 0
  },
}
</script>
