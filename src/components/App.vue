<template>
  <div id="app">
    <div class="form">
      <div class="left-side">
        <div v-if="!isSearching" class="operators">
          <button
            :class="operator == 'robi' ? 'active' : 'inactive'"
            @click="setOperator('robi')"
          >
            Robi
          </button>
          <button
            :class="operator == 'airtel' ? 'active' : 'inactive'"
            @click="setOperator('airtel')"
          >
            Airtel
          </button>
        </div>

        <h1 v-if="operator && isValidNumber && isSearching">
          {{ !isSearching ? "Number" : "Checking" }}: {{ searchNumber }}
        </h1>

        <form v-if="operator && !isSearching" @submit.prevent="searchNumbers">
          <div class="input_area">
            <input
              :class="{ valid: isValidNumber }"
              type="number"
              v-model="searchNumber"
            />
            <button type="submit">Search</button>
          </div>
        </form>
        <br />
        <button
          v-if="isSearching"
          class="btn btn-danger"
          @click="isSearching = false"
        >
          Stop Searcing
        </button>
        <br />
        <button v-if="collected_numbers.length" @click="collected_numbers = []">
          Clear History
        </button>
      </div>
      <div style="display: block">
        <h1>Available Number List</h1>
        <ul>
          <li v-for="number in collected_numbers" :key="number.simNumber">
            <span :class="{ valid: number.available }">{{
              number.simNumber
            }}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",

  data() {
    return {
      operator: null,
      searchNumber: null,
      collected_numbers: [],
      status: null,
      isSearching: null,
    };
  },

  methods: {
    setOperator(name) {
      this.operator = name; // robi or airtel
      this.searchNumber = name == "robi" ? "018866" : "01601";
    },

    async searchNumbers() {
      if (this.searchNumber && this.searchNumber.toString().length == 11) {
        let url = `https://da.robi.com.bd/api/v1/check/${this.searchNumber}/PREPAID`;

        this.status = "Checking";
        this.isSearching = true;
        await fetch(url)
          .then((res) => res.json())
          .then((res) => {
            if (res.isSuccess && !res.message) {
              this.collected_numbers.unshift({
                id: this.searchNumber,
                simNumber: this.searchNumber,
                available: true,
              });
            } else {
              this.collected_numbers.unshift({
                id: this.searchNumber,
                simNumber: this.searchNumber,
                available: false,
              });
            }
          })
          .catch((e) => console.log(e));

        if (this.isSearching) {
          this.searchNumber = `0${parseInt(this.searchNumber) + 1}`;
          this.searchNumbers();
        }
      } else {
        alert("The number must be a valid (11 digit) number");
      }
    },
  },

  computed: {
    isValidNumber() {
      return this.searchNumber.toString().length == 11;
    },
  },
};
</script>

<!-- CSS libraries -->
<style src="normalize.css/normalize.css"></style>

<!-- Global CSS -->
<style>
.form {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  background: rgb(239 255 250);
  height: 100vh;
}

.left-side {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.operators button {
  margin: 0 20px;
}

.input_area {
  display: flex;
  margin-top: 20px;
}

.form input {
  outline: none;
  border: 2px solid rgb(195 90 221);
  border-radius: 5px 0 0 5px;
  padding: 10px;
  border-right: none;
}

.input_area span {
  padding: 10px 10px;
  background: rgb(226 226 226);
  border-radius: 5px 0 0 5px;
  font-size: 20px;
}

button {
  color: white;
  background: rgb(195 90 221);
  padding: 10px 10px;
  border: none;
  outline: none;
  cursor: pointer;
  border-radius: 5px;
}

button.active {
  background: #0099d3;
}

button.btn-danger {
  background: #ff0faf;
}

button.inactive {
  background: #cdcdcd;
}

.input_area button {
  border-radius: 0 5px 5px 0;
}
button:disabled {
  background: rgb(199, 199, 199);
}

.form ul {
  height: 400px;
  overflow: scroll;
}

ul li {
  font-size: 20pt;
}

input.valid {
  border-color: green;
}

input.invalid {
  border-color: red;
  background-color: rosybrown;
}

.valid {
  color: rgb(25, 87, 255);
}
</style>